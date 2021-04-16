---
title: Migration d’un NAS local vers des partages de fichiers Azure
description: Découvrez comment utiliser Azure DataBox pour migrer des fichiers depuis un emplacement NAS (Network Attached Storage) local vers des partages de fichiers Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491670"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Utiliser DataBox pour migrer d’un NAS vers des partages de fichiers Azure

Cet article sur la migration fait partie d’une série d’articles traitant de mots clés tels que NAS et Azure DataBox. Vérifiez qu’il s’applique à votre scénario :

> [!div class="checklist"]
> * Source de données : stockage NAS (Network-Attached Storage)
> * Itinéraire de migration : NAS &rArr; DataBox &rArr; Partage de fichier Azure
> * Mise en cache de fichiers locaux : non, l’objectif final est d’utiliser les partages de fichiers Azure directement dans le cloud. Cette procédure n’implique pas l’utilisation de la fonctionnalité Azure File Sync.

Si votre scénario est différent, consultez le [tableau des guides de migration](storage-files-migration-overview.md#migration-guides).

Cet article vous guide tout au long des étapes de planification, de déploiement et de mise en réseau nécessaires à la migration de votre appliance NAS vers des partages de fichiers Azure fonctionnels. Dans ce guide, nous utiliserons Azure DataBox pour le transport de données en bloc (transport de données hors connexion).

## <a name="migration-goals"></a>Objectifs de la migration

L’objectif est de déplacer les partages que vous avez sur votre appliance NAS vers Azure et de les convertir en partages de fichiers Azure natifs que vous pouvez utiliser sans avoir besoin d’une instance Windows Server. Cette migration doit être effectuée de manière à garantir l’intégrité des données de production et la disponibilité pendant la migration. Garantir la disponibilité implique de garder les temps d’arrêt à un niveau minimal pour qu’ils respectent les fenêtres de maintenance habituelles ou ne les dépassent que légèrement.

## <a name="migration-overview"></a>Vue d’ensemble de la migration

Le processus de migration se compose de plusieurs phases : Vous devez déployer des comptes et des partages de fichiers Stockage Azure, configurer la mise en réseau, migrer à l’aide d’Azure DataBox, récupérer les modifications via RoboCopy et enfin, transférer vos utilisateurs vers les nouveaux partages de fichiers Azure. Les sections suivantes décrivent en détail les phases du processus de migration.

> [!TIP]
> Si vous revenez à cet article, utilisez la navigation de droite pour accéder à la phase de migration là où vous vous étiez arrêté.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Phase 1 : Identifier le nombre de partages de fichiers Azure dont vous avez besoin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2 : Déployer des ressources de stockage Azure

Au cours de cette phase, consultez la table de mappage de la phase 1 et utilisez-la pour configurer le nombre correct de comptes de stockage Azure et de partages de fichiers dans ceux-ci.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Phase 3 : Déterminer le nombre d’appliances Azure DataBox dont vous avez besoin

Ne suivez cette étape qu’après avoir terminé la phase précédente. À ce stade, vos ressources de stockage Azure (comptes de stockage et partages de fichiers) doivent être créées. Lors de la commande de votre DataBox, vous devez spécifier les comptes de stockage vers lesquels la DataBox déplace les données.

Au cours de cette phase, vous devez mapper les résultats du plan de migration de la phase précédente aux limites des options DataBox disponibles. Ces considérations vous aideront à établir un plan correspondant aux options DataBox que vous devez choisir et combien d’entre elles vous seront nécessaires pour déplacer vos partages NAS vers des partages de fichiers Azure.

Pour déterminer le nombre d’appareils dont vous avez besoin, tenez compte des limites importantes suivantes :

* Une DataBox Azure peut déplacer des données dans un maximum de 10 comptes de stockage. 
* Chaque option DataBox dispose de sa propre capacité utilisable. Consultez [Options de DataBox](#databox-options).

Consultez votre plan de migration pour obtenir le nombre de comptes de stockage que vous avez décidé de créer, ainsi que les partages dans chacun d’entre eux. Examinez ensuite la taille de chaque partage sur votre NAS. La combinaison de ces informations vous sera utile à des fins d’optimisation, ainsi que pour choisir appliance qui devra envoyer des données vers les comptes de stockage. Vous pouvez faire en sorte que deux appareils DataBox déplacent des fichiers dans le même compte de stockage, sans fractionner le contenu d’un partage de fichiers unique sur deux DataBox.

### <a name="databox-options"></a>Options de DataBox

Pour une migration standard, une option de DataBox, voire les trois peuvent être choisies : 

* DataBox Disks Microsoft vous enverra d’un à cinq disques SSD avec une capacité de 8 Tio chacun, pour un total maximal de 40 Tio. La capacité utilisable est d’environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez la [documentation DataBox Disks](../../databox/data-box-disk-overview.md).
* DataBox Correspond à l’option la plus courante. Une appliance DataBox robuste, qui fonctionne de façon similaire à un NAS, vous sera expédiée. Elle dispose d’une capacité utilisable de 80 Tio. Pour plus d’informations, consultez la [documentation DataBox](../../databox/data-box-overview.md).
* DataBox Heavy Cette option offre une appliance DataBox robuste sur roues, qui fonctionne de façon similaire à un NAS, avec une capacité de 1 Pio. La capacité utilisable est d’environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez [la documentation DataBox Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-temporary-windows-server"></a>Phase 4 : Configurer une instance Windows Server temporaire

En attendant l’arrivée de votre ou vos DataBox Azure, vous pouvez déjà déployer une ou plusieurs instances Windows Server dont vous aurez besoin pour exécuter des tâches RoboCopy. 

- Ces serveurs vous serviront d’abord à copier vos fichiers sur le DataBox.
- Puis ils vous serviront à récupérer les modifications qui se sont produites sur l’appliance NAS pendant la livraison de votre ou vos DataBox. Cette approche permet de réduire au minimum le temps d’arrêt côté source.

La vitesse de fonctionnement de vos tâches RoboCopy dépend principalement de ces facteurs :

* Les IOPS sur le stockage source et le stockage cible
* La bande passante réseau disponible entre eux </br> Pour en savoir plus, consultez cette section d’aide à la résolution de problèmes : [Remarques relatives à la bande passante et aux IOPS](#iops-and-bandwidth-considerations).
* La capacité de traiter rapidement des fichiers et des dossiers dans un espace de noms </br> Pour plus d’informations, consultez cette section d’aide à la résolution de problèmes : [Vitesse de traitement](#processing-speed).
* Le nombre de modifications entre les exécutions de RoboCopy </br> Pour plus d’informations, consultez cette section d’aide à la résolution de problèmes : [Éviter les tâches inutile](#avoid-unnecessary-work).

Il est important de noter les informations référencées. Elles vous serviront à décider quelle quantité de mémoire RAM et combien de threads vous fournirez à votre ou vos instances Windows Server temporaires.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Phase 5 : Préparation à l’utilisation des partages de fichiers Azure

Pour gagner du temps, vous devez passer à cette phase pendant que vous attendez la livraison de votre DataBox. Les informations obtenues lors de cette phase vous permettront de décider comment vos serveurs et utilisateurs, dans Azure et en dehors d’Azure, seront activés pour utiliser vos partages de fichiers Azure. Voici quelles sont les décisions les plus importantes à prendre :

- **Mise en réseau :** autorisez vos réseaux à acheminer le trafic SMB.
- **Authentification :** configurez les comptes de stockage Azure pour l’authentification Kerberos. AdConnect et la jonction de domaine pour votre compte de stockage permettront à vos applications et utilisateurs d’utiliser leur identité AD pour l’authentification
- **Autorisation :** les listes de contrôle d’accès au niveau du partage pour chaque partage de fichiers Azure permettent aux utilisateurs et aux groupes Active Directory d’accéder à un partage donné. Puis, une fois dans un partage de fichiers Azure, les listes de contrôle d’accès NTFS natives prendront le relais. L’autorisation basée sur les listes de contrôle d’accès des fichiers et des dossiers fonctionne alors comme sur des partages SMB locaux.
- **Continuité des activités :** l’intégration des partages de fichiers Azure dans un environnement existant implique souvent de conserver les adresses de partage existantes. Si vous n’utilisez pas déjà DFS-N (pour DFS-Namespaces, ou « Espaces de noms DFS »), nous vous conseillons de l’établir dans votre environnement. Vous pourrez ainsi conserver les adresses de partage que vos utilisateurs et scripts utilisent, sans les modifier. Vous pouvez utiliser DFS-N comme service de routage d’espace de noms pour SMB, en redirigeant les cibles DFS-Namespace vers des partages de fichiers Azure après leur migration.

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
* [Configurer DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Phase 6 : Copier des fichiers sur votre DataBox

Une fois votre DataBox reçue, vous devez la configurer dans la ligne de vue de votre appliance NAS. Suivez la documentation relative au type de DataBox que vous avez commandé.

* [Configurer Data Box](../../databox/data-box-quickstart-portal.md)
* [Configurer Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Configurer Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Selon le type de DataBox, des outils de copie DataBox peuvent être mis à votre disposition. À ce stade, ils ne sont pas recommandés pour effectuer des migrations vers des partages de fichiers Azure, car ils ne copient pas vos fichiers assez fidèlement vers la DataBox. Privilégiez RoboCopy.

La DataBox reçue est dotée de partages SMB préconfigurés disponibles pour chaque compte de stockage spécifié au moment de la commande.

* Si vos fichiers sont placés dans un partage de fichiers Azure Premium, vous disposerez d’un partage SMB par compte de stockage « stockage de fichiers » Premium.
* Si vos fichiers sont placés dans un compte de stockage standard, vous disposerez de trois partages SMB par compte de stockage standard (GPv1 et GPv2). Seuls les partages de fichiers se terminant par `_AzFiles` sont pertinents pour votre migration. Ignorez les partages d’objets blob de blocs et de pages.

Suivez les étapes décrites dans la documentation Azure DataBox :

1. [Se connecter à Data Box](../../databox/data-box-deploy-copy-data.md)
1. Copier des données sur Data Box
1. [Préparer votre DataBox pour Azure](../../databox/data-box-deploy-picked-up.md)

La documentation DataBox connexe spécifie une commande RoboCopy. Toutefois, la commande ne permet pas de préserver la fidélité complète des fichiers et des dossiers. Utilisez plutôt la commande suivante :

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* Pour en savoir plus sur les détails des différents indicateurs RoboCopy, consultez le tableau dans la section [Robocopy](#robocopy).
* Pour en savoir plus sur la façon de dimensionner correctement le nombre de threads `/MT:n`, d’optimiser la vitesse de Robocopy et de faire de Robocopy un bon adjuvant pour votre centre de données, consultez la section d’aide à la [résolution des problèmes affectant Robocopy](#troubleshoot).


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Phase 7 : Mettre à jour RoboCopy à partir de votre NAS

Une fois que votre DataBox signale que tous les fichiers et dossiers ont été placés dans les partages de fichiers Azure planifiés, vous pouvez poursuivre cette phase.
Un RoboCopy de récupération est seulement nécessaire si les données sur le NAS ont pu être modifiées depuis le démarrage de la copie vers DataBox. Dans certains scénarios où vous utilisez un partage à des fins d’archivage, vous pouvez être en mesure d’arrêter les modifications du partage sur votre NAS jusqu’à la fin de la migration. Vous pouvez aussi être en mesure de répondre aux besoins de votre entreprise en définissant les partages NAS en lecture seule pendant la migration.

Dans les cas où vous avez besoin d’un partage en lecture-écriture pendant la migration et que vous devez réduire au minimum le temps d’arrêt, il est important pour vous de terminer cette étape de récupération avant le basculement de l’accès des utilisateurs directement vers le partage de fichiers Azure.

Dans cette étape, vous allez exécuter des travaux RoboCopy pour intercepter vos partages cloud avec les dernières modifications apportées à votre NAS depuis la duplication de vos partages sur la DataBox.
Cette mise à jour RoboCopy peut se terminer rapidement ou prendre un certain temps selon le volume de modifications intervenues sur vos partages NAS.

Exécutez la première copie locale vers votre dossier Windows Server cible :

1. Identifiez le premier emplacement sur votre appliance NAS.
1. Identifiez le partage de fichiers Azure correspondant.
1. Montez le partage de fichiers Azure en tant que lecteur réseau local sur votre instance Windows Server temporaire.
1. Démarrez la copie à l’aide de RoboCopy, comme indiqué.

### <a name="mounting-an-azure-file-share"></a>Montage d’un partage de fichiers Azure

Avant de pouvoir utiliser RoboCopy, vous devez rendre le partage de fichiers Azure accessible sur SMB. Le moyen le plus simple consiste à monter le partage en tant que lecteur réseau local sur l’instance Windows Server que vous envisagez d’utiliser avec RoboCopy. 

> [!IMPORTANT]
> Avant de pouvoir monter correctement un partage de fichiers Azure sur une instance Windows Server locale, vous devez avoir terminé la phase : Préparation à l’utilisation des partages de fichiers Azure.

Lorsque vous êtes prêt, consultez l’article [Utiliser un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md) et montez le partage de fichiers Azure pour lequel vous souhaitez démarrer la tâche de récupération de NAS Robocopy.

### <a name="robocopy"></a>Robocopy

La commande RoboCopy suivante copie uniquement les différences (fichiers et dossiers mis à jour) de votre stockage NAS vers votre partage de fichiers Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> Si Robocopy a un impact sur votre environnement de production, signale un grand nombre d’erreurs ou ne progresse pas aussi vite que prévu, consultez cette [section d’aide à la résolution de problèmes](#troubleshoot).

### <a name="user-cut-over"></a>Transfert de l’utilisateur

Quand vous exécutez la commande RoboCopy pour la première fois, les utilisateurs et applications ont toujours accès aux fichiers sur l’appliance NAS et peuvent éventuellement les modifier. Il est possible que RoboCopy traite un répertoire, passe au répertoire suivant, puis qu’un utilisateur accédant à l’emplacement source (NAS) ajoute, modifie ou supprime un fichier qui ne sera pas traité durant cette exécution de RoboCopy. Il s’agit du comportement attendu.

La première exécution consiste à déplacer la majeure partie des données évolutives vers votre partage de fichiers Azure. Cette première copie peut prendre beaucoup de temps. Pour en savoir plus sur les éléments susceptibles d’affecter la vitesse de Robocopy, consultez cette [section d’aide à la résolution de problèmes](#troubleshoot).

Une fois l’exécution initiale terminée, réexécutez la commande.

Elle se termine plus rapidement la deuxième fois que vous exécutez RoboCopy pour le même partage. En effet, elle doit déplacer uniquement les éléments modifiés depuis la dernière exécution. Vous pouvez exécuter des travaux répétés pour le même partage.

Dès lors que vous considérez que le temps d’arrêt est acceptable, vous devez supprimer l’accès utilisateur à vos partages NAS. Pour ce faire, vous pouvez utiliser n’importe quelle étape empêchant les utilisateurs de modifier la structure des fichiers et des dossiers, ainsi que leur contenu. Par exemple, vous pouvez faire pointer votre DFS-Namespace vers un emplacement non existant ou modifier les listes de contrôle d’accès racine sur le partage.

Exécutez une dernière fois la commande RoboCopy pour traiter toutes les modifications qui n’ont pas encore été prises en compte.
La durée de cette dernière étape dépend de la vitesse d’analyse de RoboCopy. Vous pouvez estimer la durée d’exécution (correspondant au temps d’arrêt) en mesurant la durée de l’exécution précédente.

Créez un partage dans le dossier Windows Server et, le cas échéant, ajustez votre déploiement DFS-N pour qu’il pointe vers celui-ci. Veillez à définir les mêmes autorisations au niveau du partage que celles de votre partage SMB NAS. Si vous aviez un NAS joint à un domaine d’entreprise, les SID de l’utilisateur correspondent automatiquement à mesure que la présence des utilisateurs est vérifiée dans Active Directory et que RoboCopy copie fidèlement les fichiers et les métadonnées. Si vous avez utilisé des utilisateurs locaux sur votre emplacement NAS, vous devez recréer ces utilisateurs en tant qu’utilisateurs locaux de Windows Server et mapper les SID existants que RoboCopy a déplacés sur votre Windows Server avec les SID de vos nouveaux utilisateurs locaux de Windows Server.

Vous avez terminé la migration d’un partage/groupe de partages vers une racine ou un volume commun (selon votre mappage à partir de la phase 1)

Vous pouvez essayer d’exécuter quelques-unes de ces copies en parallèle. Nous vous recommandons de traiter l’étendue d’un partage de fichiers Azure à la fois.

## <a name="troubleshoot"></a>Dépanner

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre davantage sur les partages de fichiers Azure. Les articles suivants vous aident à comprendre les options avancées et les meilleures pratiques et contiennent également des aides pour la résolution des problèmes. Ces articles sont liés à la [documentation relative aux partages de fichiers Azure](storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](storage-files-migration-overview.md)
* [Analyser, diagnostiquer et dépanner Microsoft Azure Storage](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Considérations réseau pour l’accès direct](storage-files-networking-overview.md)
* [Sauvegarde : captures instantanées de partage de fichiers Azure](storage-snapshots-files.md)
