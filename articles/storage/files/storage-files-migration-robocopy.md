---
title: Migrer vers des partages de fichiers Azure avec RoboCopy
description: Découvrez comment migrer des fichiers depuis plusieurs emplacements vers des partages de fichiers Azure avec RoboCopy.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/12/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 76fb327eaf2f95bd96513f6382a00e29a54fe893
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462243"
---
# <a name="use-robocopy-to-migrate-to-azure-file-shares"></a>Utiliser RoboCopy pour migrer vers des partages de fichiers Azure

Cet article décrit l’utilisation de RoboCopy pour déplacer ou migrer des fichiers vers un partage de fichiers Azure. RoboCopy est un utilitaire de copie de fichiers fiable et connu qui présente un ensemble de fonctionnalités qui lui permet d’être adapté aux migrations. Il utilise le protocole SMB, qui le rend largement applicable à toute combinaison source/cible prenant en charge SMB.

> [!div class="checklist"]
> * Sources de données : toute source prenant en charge le protocole SMB, telle que le stockage NAS (Network Attached Storage), les serveurs Windows ou Linux, un autre partage de fichiers Azure et de nombreuses autres
> * Route de migration : stockage source &rArr; ordinateur Windows avec RoboCopy &rArr; partage de fichiers Azure

Il existe de nombreuses routes de migration différentes pour différentes combinaisons de sources et de déploiements. Examinez le [tableau des guides de migration](storage-files-migration-overview.md#migration-guides) pour trouver la migration qui correspond le mieux à vos besoins.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="azcopy-vs-robocopy"></a>AzCopy ou RoboCopy
AzCopy et RoboCopy sont deux outils de copie de fichiers fondamentalement différents. RoboCopy utilise n’importe quelle version du protocole SMB. AzCopy est un outil « né dans le cloud » qui peut être utilisé pour déplacer des données tant que la cible est dans le stockage Azure. AzCopy dépend d’un protocole REST.

RoboCopy, en tant qu’outil de copie approuvé basé sur Windows, prend l’avantage quand il s’agit de copier des fichiers avec une fidélité totale. RoboCopy prend en charge de nombreux scénarios de migration grâce à son ensemble complet de fonctionnalités et à son aptitude à copier des fichiers et des dossiers avec une fidélité totale. Consultez la [section sur la fidélité des fichiers dans l’article Vue d’ensemble de la migration](storage-files-migration-overview.md#migration-basics) pour en savoir plus sur l’importance de copier des fichiers avec la fidélité la plus optimale possible.

En revanche, ce n’est que récemment qu’AzCopy a été développé pour prendre en charge la copie de fichiers avec une certaine fidélité et que les premières fonctionnalités nécessaires pour être considéré comme un outil de migration ont été ajoutées. Malgré tout, il existe toujours des écarts et il peut être facile de mal comprendre les fonctionnalités lors de la comparaison des indicateurs AzCopy aux indicateurs RoboCopy.

Exemple : *RoboCopy /MIR* met la source en miroir sur la cible, ce qui signifie que les fichiers ajoutés, modifiés et supprimés sont pris en compte. Une différence importante avec *AzCopy - sync* réside dans le fait que les fichiers supprimés de la source ne seront pas supprimés de la cible. Cela rend l’ensemble de fonctionnalités de copie différentielle incomplet. AzCopy va continuer à évoluer. Pour l’instant, AzCopy n’est pas un outil recommandé pour les scénarios de migration avec les partages de fichiers Azure en tant que cible. 

## <a name="migration-goals"></a>Objectifs de la migration

L’objectif est de déplacer les données des emplacements de partage de fichiers existants vers Azure. Dans Azure, vous allez stocker vos données dans des partages de fichiers Azure natifs que vous pouvez utiliser sans avoir besoin d’un serveur Windows. Cette migration doit être effectuée de manière à garantir l’intégrité des données de production et la disponibilité pendant la migration. Garantir la disponibilité implique de garder les temps d’arrêt à un niveau minimal pour qu’ils respectent les fenêtres de maintenance habituelles ou ne les dépassent que légèrement.

## <a name="migration-overview"></a>Vue d’ensemble de la migration

Le processus de migration se compose de plusieurs phases : Vous devez déployer des comptes de stockage et partages de fichiers Azure. De plus, vous allez configurer les réseaux, envisager un déploiement d’espace de noms DFS (DFS-N) ou mettre à jour le vôtre. Une fois qu’il est temps de copier les données actuelles, vous devez prendre en compte les exécutions répétées et différentielles de RoboCopy pour réduire les temps d’arrêt, et enfin, diriger vos utilisateurs vers les nouveaux partages de fichiers Azure créés. Les sections suivantes décrivent en détail les phases du processus de migration.

> [!TIP]
> Si vous revenez à cet article, utilisez la navigation de droite pour accéder à la phase de migration là où vous vous étiez arrêté.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Phase 1 : Identifier le nombre de partages de fichiers Azure dont vous avez besoin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2 : Déployer des ressources de stockage Azure

Au cours de cette phase, consultez la table de mappage de la phase 1 et utilisez-la pour configurer le nombre correct de comptes de stockage Azure et de partages de fichiers dans ceux-ci.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-preparing-to-use-azure-file-shares"></a>Phase 3 : Préparation à l’utilisation des partages de fichiers Azure

Les informations obtenues lors de cette phase vous permettront de décider comment vos serveurs et utilisateurs, dans Azure et en dehors d’Azure, seront activés pour utiliser vos partages de fichiers Azure. Voici quelles sont les décisions les plus importantes à prendre :

- **Mise en réseau :** autorisez vos réseaux à acheminer le trafic SMB.
- **Authentification :** configurez les comptes de stockage Azure pour l’authentification Kerberos. AdConnect et la jonction de domaine pour votre compte de stockage permettront à vos applications et utilisateurs d’utiliser leur identité AD pour l’authentification
- **Autorisation :** les listes de contrôle d’accès au niveau du partage pour chaque partage de fichiers Azure permettent aux utilisateurs et aux groupes Active Directory d’accéder à un partage donné. Puis, une fois dans un partage de fichiers Azure, les listes de contrôle d’accès NTFS natives prendront le relais. L’autorisation basée sur les listes de contrôle d’accès des fichiers et des dossiers fonctionne alors comme sur des partages SMB locaux.
- **Continuité des activités :** l’intégration des partages de fichiers Azure dans un environnement existant implique souvent de conserver les adresses de partage existantes. Si vous n’utilisez pas déjà [DFS-Namespaces](files-manage-namespaces.md), nous vous conseillons de l’établir dans votre environnement. Vous pourrez ainsi conserver les adresses de partage que vos utilisateurs et scripts utilisent, sans les modifier. DFS-N fournit un service de routage d’espace de noms pour SMB, en redirigeant les clients vers des partages de fichiers Azure.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Cette vidéo montre comment exposer directement et de façon sécurisée les partages de fichiers Azure aux travailleurs de l’information et aux applications, en cinq étapes simples.</br>
        La vidéo fait référence à une documentation dédiée pour certains sujets :

* [Vue d’ensemble de l’identité](storage-files-active-directory-overview.md)
* [Comment joindre un domaine à un compte de stockage](storage-files-identity-auth-active-directory-enable.md)
* [Présentation de la mise en réseau pour les partages de fichiers Azure](storage-files-networking-overview.md)
* [Comment configurer des points de terminaison publics et privés](storage-files-networking-endpoints.md)
* [Comment configurer un VPN site à site](storage-files-configure-s2s-vpn.md)
* [Comment configurer un VPN point à site Windows](storage-files-configure-p2s-vpn-windows.md)
* [Comment configurer un VPN point à site Linux](storage-files-configure-p2s-vpn-linux.md)
* [Procédure de configuration du transfert DNS](storage-files-networking-dns.md)
* [Configurer DFS-N](files-manage-namespaces.md)
   :::column-end:::
:::row-end:::

### <a name="mounting-an-azure-file-share"></a>Montage d’un partage de fichiers Azure

Avant de pouvoir utiliser RoboCopy, vous devez rendre le partage de fichiers Azure accessible sur SMB. Le moyen le plus simple consiste à monter le partage en tant que lecteur réseau local sur l’instance Windows Server que vous envisagez d’utiliser avec RoboCopy. 

> [!IMPORTANT]
> Avant de pouvoir monter correctement un partage de fichiers Azure sur une instance Windows Server locale, vous devez avoir terminé la phase 3 : Préparation à l’utilisation des partages de fichiers Azure.

Une fois que vous êtes prêt, consultez l’[article de procédure sur l’utilisation d’un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md). Ensuite, montez le partage de fichiers Azure pour lequel vous souhaitez démarrer RoboCopy.

## <a name="phase-4-robocopy"></a>Phase 4 : RoboCopy

La commande RoboCopy suivante copie uniquement les différences (fichiers et dossiers mis à jour) de votre stockage source vers votre partage de fichiers Azure.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> Si Robocopy a un impact sur votre environnement de production, signale un grand nombre d’erreurs ou ne progresse pas aussi vite que prévu, consultez cette [section d’aide à la résolution de problèmes](#troubleshoot-and-optimize).

## <a name="phase-5-user-cut-over"></a>Phase 5 : Transfert de l’utilisateur

Quand vous exécutez la commande RoboCopy pour la première fois, les utilisateurs et applications ont toujours accès aux fichiers sur la source de votre migration et peuvent éventuellement les modifier. Il est possible que RoboCopy traite un répertoire, passe au répertoire suivant, puis qu’un utilisateur accédant à l’emplacement source ajoute, modifie ou supprime un fichier qui ne sera pas traité durant cette exécution de RoboCopy. Il s’agit du comportement attendu.

La première exécution consiste à déplacer la majeure partie des données évolutives vers votre partage de fichiers Azure. Cette première copie peut prendre beaucoup de temps. Pour en savoir plus sur les éléments susceptibles d’affecter la vitesse de Robocopy, consultez cette [section d’aide à la résolution de problèmes](#troubleshoot-and-optimize).

Une fois l’exécution initiale terminée, réexécutez la commande.

Elle se termine plus rapidement la deuxième fois que vous exécutez RoboCopy pour le même partage. En effet, elle doit déplacer uniquement les éléments modifiés depuis la dernière exécution. Vous pouvez exécuter des travaux répétés pour le même partage.

Dès lors que vous considérez que le temps d’arrêt est acceptable, vous devez supprimer l’accès utilisateur à vos partages sources. Pour ce faire, vous pouvez utiliser n’importe quelle étape empêchant les utilisateurs de modifier la structure des fichiers et des dossiers, ainsi que leur contenu. Par exemple, vous pouvez faire pointer votre DFS-Namespace vers un emplacement non existant ou modifier les listes de contrôle d’accès sur chaque partage.

Exécutez une dernière fois la commande RoboCopy pour traiter toutes les modifications qui n’ont pas encore été prises en compte.
La durée de cette dernière étape dépend de la vitesse d’analyse de RoboCopy. Vous pouvez estimer la durée d’exécution (correspondant au temps d’arrêt) en mesurant la durée de l’exécution précédente.

Dans une section précédente, vous avez configuré vos utilisateurs pour qu’ils [accèdent au partage avec leur identité](#phase-3-preparing-to-use-azure-file-shares) et avez dû établir une stratégie leur permettant d’[utiliser les chemins établis vers vos nouveaux partages de fichiers Azure (DFS-N)](files-manage-namespaces.md).

Vous pouvez essayer d’exécuter quelques-unes de ces copies entre différents partages sources et cibles en parallèle. Lorsque vous procédez ainsi, gardez en tête le débit de votre réseau et le ratio du nombre de threads pour ne pas surcharger le système.

## <a name="troubleshoot-and-optimize"></a>Dépanner et optimiser

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre davantage sur les partages de fichiers Azure. Les articles suivants vous aident à comprendre les options avancées et les meilleures pratiques et contiennent également des aides pour la résolution des problèmes. Ces articles sont liés à la [documentation relative aux partages de fichiers Azure](storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](storage-files-migration-overview.md)
* [Sauvegarde : captures instantanées de partage de fichiers Azure](storage-snapshots-files.md)
* [Utilisation de DFS Namespaces avec Azure Files](files-manage-namespaces.md)
