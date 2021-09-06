---
title: Créer un point de terminaison du serveur Azure File Sync
description: Comprendre les options lors de la création de points de terminaison de serveur et apprendre comment les appliquer au mieux à votre situation.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: b981ae7394e070d4da793fddccdd52e4350bde86
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799122"
---
# <a name="create-an-azure-file-sync-server-endpoint"></a>Créer un point de terminaison du serveur Azure File Sync

Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur. Un point de terminaison de serveur doit remplir les conditions suivantes :

- Un point de terminaison de serveur doit être un chemin d'accès sur un serveur inscrit (plutôt que sur un partage monté). Le stockage attaché au réseau (NAS) n’est pas pris en charge.
- Même si le point de terminaison de serveur se trouve sur le volume système, les points de terminaison de serveur situés sur le volume système ne peuvent pas utiliser la hiérarchisation cloud.
- La modification du chemin d’accès ou de la lettre de lecteur après l’établissement d’un point de terminaison de serveur sur un volume n’est pas prise en charge. Vérifiez que vous utilisez un chemin d’accès approprié avant de créer le point de terminaison de serveur.
- Un serveur inscrit peut prendre en charge plusieurs points de terminaison de serveur, cependant un groupe de synchronisation ne peut avoir qu'un seul point de terminaison de serveur par serveur inscrit à un moment donné. Les autres points de terminaison de serveur du groupe de synchronisation doivent se trouver sur des serveurs inscrits distincts.
- Plusieurs points de terminaison de serveur peuvent se trouver sur le même volume si leurs espaces de noms ne se chevauchent pas (par exemple, F:\sync1 et F:\sync2) et si chaque point de terminaison est synchronisé avec un groupe de synchronisation unique. 

Cet article vous aide à comprendre les options et les décisions nécessaires pour créer un nouveau point de terminaison de serveur et démarrer la synchronisation. Pour que cela fonctionne, vous devez avoir terminé [la planification de votre déploiement Azure File Sync](file-sync-planning.md) et avoir déployé les [ressources nécessaires dans les étapes précédentes](file-sync-deployment-guide.md) pour créer un point de terminaison de serveur.

## <a name="prerequisites"></a>Conditions préalables requises

Pour créer un point de terminaison de serveur, vous devez d’abord vérifier que les critères suivants sont remplis : 
- L’agent de synchronisation de fichiers Azure est installé le serveur et ce dernier a été inscrit. Vous trouverez des instructions pour l’installation de l’agent Azure File Sync dans l’article [Inscrire/désinscrire un serveur à l’aide d’Azure File Sync](file-sync-server-registration.md). 
- Vérifiez qu’un service de synchronisation de stockage a été déployé. Consultez le [Guide pratique pour déployer Azure File Sync](file-sync-deployment-guide.md) pour en savoir plus sur le déploiement d’un service de synchronisation de stockage. 
- Vérifiez qu’un groupe de synchronisation a été déployé. Découvrez comment [Créer un groupe de synchronisation](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint).
- Vérifiez que le serveur est connecté à Internet et qu’Azure est accessible. Azure File Sync utilise le port 443 pour toutes les communications entre le serveur et le service cloud.

## <a name="create-a-server-endpoint"></a>Créer un point de terminaison de serveur

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

### <a name="cloud-tiering-section"></a>Section hiérarchisation cloud

Lorsque vous créez un nouveau point de terminaison de serveur, vous pouvez choisir la fonctionnalité de hiérarchisation cloud de Azure File Sync. Les options de la section **Hiérarchisation cloud** peuvent être modifiées ultérieurement. Toutefois, les différentes options de la section suivante sont disponibles selon que la hiérarchisation cloud est activée ou non pour votre nouveau point de terminaison de serveur.

Reportez-vous à l’article sur la [Hiérarchisation cloud](file-sync-cloud-tiering-overview.md) qui couvre les principes de base, [les stratégies](file-sync-cloud-tiering-policy.md) et les meilleures pratiques en détail. 

### <a name="initial-sync-section"></a>Section synchronisation initiale

La section **Synchronisation initiale** est disponible uniquement pour le premier point de terminaison de serveur dans un groupe de synchronisation. Pour tout point de terminaison de serveur supplémentaire, consultez la [Section téléchargement initial](#initial-download-section).

Il existe deux comportements de synchronisation initiale fondamentalement différents :

:::row:::
    :::column:::
        **Fusionner**
    :::column-end:::
    :::column:::
        **Chargement faisant autorité**
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Fusionner est l’option standard, sélectionnée par défaut. Vous devez conserver la sélection sur **Fusionner**, sauf pour certains scénarios de migration. 
* Quand vous joignez un emplacement de serveur, dans la plupart des scénarios, l’emplacement du serveur ou le partage cloud est vide. Dans ce cas, **Fusionner** est le comportement approprié et entraîne des résultats attendus. 
* Lorsque les deux emplacements contiennent des fichiers et des dossiers, les espaces de noms sont fusionnés. S’il existe des noms de fichiers ou de dossiers sur le serveur qui existent également dans le partage cloud, il y aura un conflit de synchronisation. [Les conflits sont résolus automatiquement](../files/storage-files-faq.md#afs-conflict-resolution). </br> </br> Dans l’option **Fusion**, il existe une sélection à effectuer pour définir la façon dont le contenu du partage de fichiers Azure arrive initialement sur le serveur. Cette sélection n’a aucun impact si le partage de fichiers Azure est vide. Vous trouverez plus de détails dans le paragraphe à venir : [Téléchargement initial](#initial-download-section)
    :::column-end:::
    :::column:::
        Le **chargement faisant autorité** est une option de synchronisation initiale réservée à un scénario de migration spécifique. La synchronisation du même chemin d’accès de serveur que celui utilisé pour amorcer le partage cloud avec par exemple Azure Data Box. Dans ce cas, le cloud et les emplacements des serveurs ont essentiellement les mêmes données, mais le serveur est légèrement plus récent. Les utilisateurs ont conservé des modifications pendant que Data Box était dans le transport. Ce scénario de migration appelle ensuite pour mettre à jour le cloud en toute transparence avec les modifications sur le serveur (les plus récentes) sans générer de conflits. Le serveur est donc l’autorité de la forme de l’espace de noms et Data Box a été utilisé pour éviter le chargement initial à grande échelle à partir du serveur. Le chargement faisant autorité du serveur permet une adoption sans temps d’arrêt du cloud, même lorsqu’un mécanisme de transport de données hors connexion a été utilisé pour amorcer le stockage cloud.
    :::column-end:::
:::row-end:::

Un point de terminaison de serveur peut uniquement effectuer l’approvisionnement avec l’option de chargement faisant autorité, lorsque l’emplacement du serveur contient des données. Ce bloc est destiné à éviter les erreurs de configuration accidentelles. Le chargement faisant autorité fonctionne comme RoboCopy /MIR. Ce mode reflète la source dans la cible. La source est le serveur AFS et la cible est le partage cloud. Le chargement faisant autorité met en forme la cible dans l’image de la source. 

* Les fichiers et dossiers nouveaux ou mis à jour seront téléchargés à partir du serveur.
* Les fichiers et dossiers qui n’existent pas (ou plus) sur le serveur seront supprimés du partage cloud.
* Métadonnées : seules les modifications apportées aux fichiers et aux dossiers sur le serveur seront déplacées vers le partage cloud en tant que mises à jour des métadonnées.
* Des fichiers et des dossiers peuvent exister sur le serveur et le partage cloud. Toutefois, certains fichiers ou dossiers peuvent avoir modifié leur répertoire parent sur le serveur depuis l’amorçage du partage de fichiers Azure. Ces fichiers et dossiers seront purgés du partage cloud et téléchargés à nouveau. Pour cette raison, il est préférable d’éviter de restructurer votre espace de noms à une plus grande échelle pendant une migration.

### <a name="initial-download-section"></a>Section de téléchargement initial

La section de **Téléchargement initial** est disponible pour le deuxième point de terminaison de serveur, ainsi que les suivants, dans un groupe de synchronisation. Le [premier point de terminaison de serveur dans un groupe de synchronisation dispose d’options supplémentaires](#initial-sync-section) relatives à la migration avec Azure Data Box. Ces options ne s’appliquent pas si ce point de terminaison de serveur n’est pas le premier dans votre groupe de synchronisation.

> [!NOTE]
> La sélection d’une option de téléchargement initiale n’a aucun impact si le partage de fichiers Azure est vide.

Dans le cadre de cette section, vous pouvez choisir la façon dont le contenu du partage de fichiers Azure arrive initialement sur le serveur :

:::row:::
    :::column:::
        :::image type="content" source="media/file-sync-server-endpoint-create/initial-download-options.png" alt-text="Image décrivant les options de l’Assistant Création d’un point de terminaison de serveur sur le Portail Azure." lightbox="media/file-sync-server-endpoint-create/initial-download-options-expanded.png":::
    :::column-end:::
    :::column:::
* **Espace de noms uniquement** </br> Permet d’importer uniquement la structure de fichiers et de dossiers du partage de fichiers Azure sur le serveur local. Aucun contenu de fichier n’est téléchargé. Cette option est la valeur par défaut si vous avez précédemment activé la hiérarchisation cloud pour ce nouveau point de terminaison de serveur.
* **Espace de noms en premier, puis contenu** </br> Pour une disponibilité plus rapide des données, votre espace de noms est d’abord arrêté, indépendamment de vos paramètres de hiérarchisation cloud. Une fois que l’espace de noms est disponible sur le serveur, le contenu du fichier est ensuite rappelé depuis le cloud vers le serveur. Le rappel se produit en fonction du timestamp de la dernière modification de chaque fichier. S’il n’y a pas suffisamment d’espace sur le volume du serveur, les fichiers restants resteront des fichiers hiérarchisés. Cette option est la valeur par défaut si vous n’avez pas activé la hiérarchisation cloud pour ce point de terminaison de serveur.
* **Éviter les fichiers hiérarchisés** </br> Cette option télécharge chaque fichier dans son intégralité avant que le fichier ne s’affiche dans le dossier sur le serveur. Cette option permet d’éviter qu’un fichier hiérarchisé existe sur le serveur. Un élément d’espace de noms et le contenu d’un fichier sont toujours présents en même temps. Évitez cette option si la récupération d’urgence rapide à partir du cloud est la raison pour laquelle vous créez un point de terminaison de serveur. Si vous avez des applications qui requièrent que les fichiers complets soient présents et ne tolèrent pas les fichiers hiérarchisés dans leurs espaces de noms, c’est idéal. Cette option n’est pas disponible si vous utilisez la hiérarchisation cloud pour votre nouveau point de terminaison de serveur.
    :::column-end:::
:::row-end:::

Une fois que vous avez sélectionné une option de téléchargement initiale, vous ne pouvez plus la modifier après avoir confirmé la création du point de terminaison de serveur. La façon dont les fichiers s’affichent sur le serveur à la fin du téléchargement initial dépend de votre utilisation de la fonctionnalité de hiérarchisation cloud et de la nécessité ou non de [rappeler de manière proactive les modifications dans le cloud](file-sync-cloud-tiering-overview.md#proactive-recalling). Ceci est une fonctionnalité utile pour les groupes de synchronisation avec plusieurs points de terminaison de serveur dans différents emplacements géographiques.

* **La hiérarchisation cloud est activée** </br> Les fichiers nouveaux et modifiés des autres points de terminaison de serveur s’affichent sous forme de fichiers hiérarchisés sur ce point de terminaison de serveur. Ces modifications sont uniquement des fichiers complets si vous avez opté pour un [rappel proactif](file-sync-cloud-tiering-overview.md#proactive-recalling) des modifications apportées au partage de fichiers Azure par d’autres points de terminaison de serveur.
*  **La hiérarchisation cloud est désactivée** </br> Les fichiers nouveaux et modifiés des autres points de terminaison de serveur s’affichent sous forme de fichiers complets sur ce point de terminaison de serveur. Ils n’apparaissent pas sous forme de fichiers hiérarchisés avant d’être rappelés. Les fichiers hiérarchisés avec la hiérarchisation cloud désactivée sont une fonctionnalité de récupération d’urgence rapide et s’affichent uniquement pendant l’approvisionnement initial.


## <a name="next-steps"></a>Étapes suivantes

Il y a bien d’autres choses à apprendre sur les partages de fichiers Azure et Azure File Sync. Les articles suivants vous aideront à comprendre les options avancées et les bonnes pratiques. Vous verrez également comment résoudre les problèmes. Ces articles contiennent des liens vers la [documentation sur les partages de fichiers Azure](../files/storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](../files/storage-files-migration-overview.md)
* [Planification d’un déploiement d’Azure File Sync](../file-sync/file-sync-planning.md)
* [Créer un partage de fichiers](../files/storage-how-to-create-file-share.md)
* [Résoudre les problèmes d’Azure File Sync](../file-sync/file-sync-troubleshoot.md)
