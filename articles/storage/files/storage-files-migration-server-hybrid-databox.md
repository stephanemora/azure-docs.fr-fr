---
title: Migrer des données vers Azure File Sync avec Azure Data Box
description: Migre les données en bloc qui sont compatibles avec Azure File Sync. Évite les conflits de fichiers et intercepte votre partage de fichiers avec les dernières modifications apportées sur le serveur pour une migration cloud sans temps d’arrêt.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1fc3f423c1ca02c6acb995ab4469d7d33607000f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255541"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>Migrer des données hors connexion vers Azure File Sync avec Azure Data Box

Comme d’autres, cet article relatif à la migration comporte les mots clés Azure File Sync et Azure Data Box. Vérifiez qu’il s’applique à votre scénario :

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="Affichage des trois étapes séquentielles décrites dans ce guide de migration. La colonne en regard de l’image les décrit en détail." lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * Source de données : Windows Server 2012 R2 ou version ultérieure, où Azure File Sync sera installé et pointera vers l’ensemble de fichiers d’origine.
        > * Itinéraire de Migration : Windows Server 2012 R2 ou plus récent &rArr; Data Box &rArr; Synchronisation du partage de fichiers Azure &rArr; avec l’emplacement du fichier d’origine Windows Server
        > * Mise en cache de fichiers locaux : Oui, l’objectif final est un déploiement de Azure File Sync qui synchronise les fichiers à partir de leur emplacement actuel. 
        :::column-end:::
:::row-end:::

L’utilisation d’Azure Data Box est un chemin viable pour déplacer la majeure partie des données de votre serveur Windows Server local vers des partages de fichiers Azure distincts, puis, si vous le souhaitez, pour ajouter Azure File Sync sur le serveur source d’origine.

Il existe différents chemins de migration à votre disposition, il est important de suivre celui qui convient :

* Vos données résident sur Windows Server 2012 R2 ou version ultérieure et vous envisagez d’installer AFS sur ce serveur et de synchroniser l’emplacement d’origine. Dans ce scénario, vous ne souhaitez pas télécharger tous les fichiers et utiliser Data Box à la place, puis utiliser la synchronisation de fichiers pour les modifications en cours. S’il s’agit de votre scénario, cet article décrit votre chemin de migration.
* Vous avez des données sur une source où vous ne pouvez *pas* installer AFS. un NAS (stockage attaché au réseau) par exemple ou sur un autre serveur. Vous allez plutôt créer un nouveau serveur vide et utiliser Azure File Sync sur ce serveur. Si ceci est votre scénario, il ne s’agit pas du bon guide de migration pour vous. Consultez plutôt : [Migrer à partir du NAS via Data Box vers Azure File Sync](../files/storage-files-migration-nas-hybrid-databox.md) ou recherchez le guide le plus adapté à votre scénario sur la page [Vue d’ensemble de la migration](../files/storage-files-migration-overview.md).
* Pour tous les autres scénarios, consultez le [tableau des guides de migration de partage de fichiers Azure](../files/storage-files-migration-overview.md). Cette page de vue d’ensemble fournit un bon point de départ pour tous les scénarios de migration.
 
## <a name="migration-overview"></a>Vue d’ensemble de la migration

Le processus de migration se compose de plusieurs phases : Plusieurs actions sont nécessaires :
- Déployer des comptes de stockage et des partages de fichiers.
- Déployer un ou plusieurs appareils Azure Data Box pour déplacer les données à partir de votre Windows Server 2012 R2 ou version ultérieure. 
- Configurer Azure File Sync avec le chargement faisant autorité.

Les sections suivantes décrivent en détail les phases du processus de migration.

> [!TIP]
> Si vous revenez à cet article, utilisez la navigation à droite de l’écran pour accéder à la phase de migration là où vous vous étiez arrêté.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Phase 1 : Déterminer le nombre de partages de fichiers Azure dont vous avez besoin

Avec ce guide de migration, vous devez continuer à utiliser le stockage en attachement direct (DAS) local qui contient vos fichiers. Les Data Box seront alimentés à partir de cet emplacement et Azure File Sync sera également configuré à cet emplacement. Le NAS (stockage attaché au réseau) ne fonctionne pas avec ce chemin de migration.

Vous déterminez les synchronisations en configurant les *groupes de synchronisation* Azure File Sync qui déterminent l’emplacement de synchronisation d’un ensemble de fichiers. Chaque groupe de synchronisation a au moins un emplacement de serveur, appelé *point de terminaison de serveur* et un partage de fichiers Azure, appelé *point de terminaison cloud*. 

Vous pouvez synchroniser les sous-chemins d’un ensemble de fichiers à chaque partage de fichiers Azure. Cela signifie que vous pouvez configurer plusieurs groupes de synchronisation pour couvrir complètement un ensemble de fichiers. Le reste de la section décrit vos options. Si vous avez besoin de restructurer vos données, vous devez effectuer la première étape, avant de poursuivre ce guide, de commander un Data Box ou de configurer la synchronisation. 

> [!CAUTION]
> Il est impératif que votre structure de fichiers et de dossiers corresponde à celle que vous souhaitez garder à long terme, avant de commencer la migration. Évitez toute restructuration inutile des dossiers pendant la migration. Cela réduira les effets positifs de l’utilisation de Azure Data Box pour le transport en bloc initial de fichiers vers Azure.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2 : Déployer des ressources de stockage Azure

Au cours de cette phase, consultez la table de mappage de la phase 1 et utilisez-la pour configurer le nombre correct de comptes de stockage Azure et de partages de fichiers dans ceux-ci.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>Phase 3 : Déterminer le nombre d’appliances Azure Data Box dont vous avez besoin

Démarrez cette étape uniquement après avoir terminé la phase précédente. À ce stade, vos ressources de stockage Azure (comptes de stockage et partages de fichiers) doivent être créées. Quand vous commandez votre Data Box, vous devez spécifier les comptes de stockage dans lesquels la Data Box déplace les données.

Au cours de cette phase, vous devez mapper les résultats du plan de migration de la phase précédente aux limites des options Data Box disponibles. Ces considérations vous aideront à établir un plan correspondant aux options Data Box que vous devez choisir et combien d’entre elles vous seront nécessaires pour déplacer vos partages NAS vers des partages de fichiers Azure.

Pour déterminer le nombre d’appareils dont vous avez besoin et leurs types, tenez compte des limites importantes suivantes :

* Une appliance Azure Data Box peut déplacer des données dans un maximum de 10 comptes de stockage. 
* Chaque option Data Box a sa propre capacité utilisable. Consultez [Options Data Box](#data-box-options).

Consultez votre plan de migration pour trouver le nombre de comptes de stockage que vous avez décidé de créer, ainsi que les partages dans chacun d’entre eux. Examinez ensuite la taille de chaque partage sur votre NAS. La combinaison de ces informations vous sera utile à des fins d’optimisation, ainsi que pour choisir appliance qui devra envoyer des données vers les comptes de stockage. Deux appareils Data Box peuvent déplacer des fichiers dans le même compte de stockage, mais ne répartissent pas le contenu d’un partage de fichiers unique sur deux Data Box.

### <a name="data-box-options"></a>Options Data Box

Pour une migration standard, choisissez une option ou une combinaison des options Data Box suivantes : 

* **Data Box Disk**.
  Microsoft vous enverra entre un et cinq disques SSD avec une capacité de 8 Tio chacun, pour un total maximal de 40 Tio. La capacité utilisable est environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez la [documentation de Data Box Disk](../../databox/data-box-disk-overview.md).
* **Data Box**.
  Cette option est la plus courante. Microsoft vous enverra une appliance Data Box renforcée qui fonctionne de façon similaire à un NAS. Elle dispose d’une capacité utilisable de 80 Tio. Pour plus d’informations, consultez la [documentation de Data Box](../../databox/data-box-overview.md).
* **Data Box Heavy**.
  Cette option comprend une appliance Data Box renforcée sur roues qui fonctionne de façon similaire à un NAS. Sa capacité est de 1 Pio. La capacité utilisable est environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez la [documentation de Data Box Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-copy-files-onto-your-data-box"></a>Phase 4 : Copier des fichiers sur votre Data Box

Quand votre Data Box arrive, vous devez la configurer dans la ligne de mire de votre appliance NAS. Suivez la documentation d’installation pour le type de Data Box que vous avez commandé :

* [Configurer Data Box](../../databox/data-box-quickstart-portal.md).
* [Configurer Data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
* [Configurer Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md).

Selon le type de Data Box, des outils de copie Data Box peuvent être disponibles. À ce stade, nous ne les recommandons pas pour des migrations vers des partages de fichiers Azure, car ils ne copient pas vos fichiers assez fidèlement sur la Data Box. Utilisez Robocopy à la place.

Quand votre Data Box arrive, elle dispose de partages SMB préprovisionnés pour chaque compte de stockage spécifié au moment de la commande.

* Si vos fichiers sont placés dans un partage de fichiers Azure Premium, vous disposerez d’un partage SMB par compte de stockage « stockage de fichiers » Premium.
* Si vos fichiers sont placés dans un compte de stockage standard, vous disposerez de trois partages SMB par compte de stockage standard (GPv1 et GPv2). Seuls les partages de fichiers se terminant par `_AzFiles` sont pertinents pour votre migration. Ignorez les partages d’objets blob de blocs et de pages.

Suivez les étapes décrites dans la documentation Azure Data Box :

1. [Connectez-vous à Data Box](../../databox/data-box-deploy-copy-data.md).
1. Copiez des données sur Data Box.
1. [Préparez votre Data Box pour le chargement sur Azure](../../databox/data-box-deploy-picked-up.md).

La documentation Data Box connexe spécifie une commande Robocopy. Cette commande ne convient pas pour préserver les fichiers et les dossiers avec fidélité. Utilisez plutôt la commande suivante :

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>Phase 5 : Déployer la ressource cloud Azure File Sync

Avant de poursuivre, attendez que tous vos fichiers soient arrivés dans les partages de fichiers Azure qui conviennent. Le processus de transmission et d’ingestion des données Data Box prend du temps.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>Phase 6 : Déployer l’agent Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>Phase 7 : Configurer Azure File Sync sur le Windows Server existant

Votre instance Windows Server locale inscrite doit être prête et connectée à Internet pour ce processus.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [![Une section Portail Azure de l’Assistant Création d’un point de terminaison de serveur s’affiche. Une case à cocher correspondant au scénario d’amorçage du partage de fichiers Azure avec des données est mise en surbrillance. Cochez cette case si vous connectez AFS au même emplacement local que celui sur lequel vous avez copié Data Box précédemment.](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Une fois que vous êtes dans l’Assistant **Créer un point de terminaison de serveur**, utilisez la case à cocher fournie sous le chemin d’accès au dossier. Effectuez cette sélection uniquement si vous avez entré un chemin d’accès qui pointe vers la même structure de fichiers et de dossiers que dans le partage de fichiers Azure (où Data Box a déplacé les fichiers et les dossiers pour cet espace de noms). </br> </br> En cas d’incompatibilité de la hiérarchie des dossiers, vous constaterez des différences qui ne peuvent pas être résolues automatiquement. Évitez toute incohérence, autrement votre investissement dans le processus de Data Box ne vous gratifiera d’aucun avantage. Toutes les données seront supprimées dans le partage de fichiers Azure. Toutes les données devront être téléchargées à partir du serveur local. Les structures de répertoires doivent correspondre pour tirer parti d’une migration en bloc avec Azure Data Box et d’une mise à jour transparente du partage cloud avec les dernières modifications apportées par le serveur.
:::column-end:::
:::row-end:::

> [!NOTE]
> L’activation de cette case à cocher permet de définir le mode de **synchronisation initiale** pour *remplacer les fichiers et dossiers faisant autorité dans le partage de fichiers Azure par le contenu du chemin d’accès de ce serveur.* Cette option est disponible uniquement pour le premier point de terminaison de serveur dans un groupe de synchronisation.

Une fois que vous avez configuré le chargement faisant autorité pour ce nouveau point de terminaison de serveur, vous pouvez éventuellement activer la hiérarchisation cloud.

La hiérarchisation cloud désigne la fonctionnalité Azure File Sync qui permet au serveur local d’avoir moins de capacité de stockage que ce qui est stocké dans le cloud, tout en ayant l’espace de noms complet disponible. Les données intéressantes localement sont également mises en cache localement pour des performances d’accès rapides. La hiérarchisation cloud est facultative. Vous pouvez la définir individuellement pour chaque point de terminaison de serveur Azure File Sync. Utilisez cette fonctionnalité pour obtenir une empreinte de stockage fixe en local, tout en offrant aux utilisateurs un cache de performances local et en stockant les données de refroidissement dans le cloud.

Pour en savoir plus, consultez la [vue d’ensemble de la hiérarchisation cloud](../file-sync/file-sync-cloud-tiering-overview.md) ou examinez de plus près les différentes [stratégies de hiérarchisation cloud](../file-sync/file-sync-cloud-tiering-policy.md) que vous pouvez utiliser pour ajuster ce qui est mis en cache/hiérarchisé sur le serveur local.

## <a name="complete-your-migration"></a>Terminer votre migration

Après la création d’un point de terminaison de serveur, la synchronisation fonctionne. Toutefois, la synchronisation doit énumérer (découvrir) les fichiers et dossiers que vous avez déplacés par le biais d’Azure Data Box dans le partage de fichiers Azure. Selon la taille de l’espace de noms, la synchronisation des dernières modifications du serveur sur le cloud peut prendre beaucoup de temps. Vos utilisateurs ne sont pas affectés et peuvent continuer à travailler avec les données sur le serveur. Cette stratégie réalise une migration Cloud sans temps d’arrêt.

Pour tous les partages de fichiers/emplacements de serveur Azure à configurer pour la synchronisation, effectuez de nouveau les étapes permettant de créer des groupes de synchronisation et d’ajouter les dossiers serveur correspondants comme points de terminaison de serveur. Vous avez utilisé Azure Data Box pour déplacer vos fichiers dans plusieurs partages de fichiers Azure. La migration est terminée une fois que vous avez créé tous les points de terminaison de serveur qui connectent vos données locales à ces partages de fichiers Azure.

## <a name="next-steps"></a>Étapes suivantes

Il y a bien d’autres choses à apprendre sur les partages de fichiers Azure et Azure File Sync. Les articles suivants vous aideront à comprendre les options avancées et les bonnes pratiques. Vous verrez également comment résoudre les problèmes. Ces articles contiennent des liens vers la [documentation sur les partages de fichiers Azure](storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](storage-files-migration-overview.md)
* [Planification d’un déploiement de synchronisation de fichiers Azure](../file-sync/file-sync-planning.md)
* [Créer un partage de fichiers](storage-how-to-create-file-share.md)
* [Résoudre les problèmes d’Azure File Sync](../file-sync/file-sync-troubleshoot.md)
