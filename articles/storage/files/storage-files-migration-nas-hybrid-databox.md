---
title: Migration NAS locale vers Azure File Sync via Azure DataBox
description: Découvrez comment migrer des fichiers d’un emplacement NAS (Network Attached Storage) vers un déploiement de cloud hybride à l’aide d’Azure File Sync via Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583523"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Utiliser DataBox pour migrer de Network Attached Storage (NAS) vers un déploiement de cloud hybride avec Azure File Sync

Comme d’autres, cet article relatif à la migration comporte les mots clés NAS, Azure File Sync et Azure DataBox. Vérifiez qu’il s’applique à votre scénario :

> [!div class="checklist"]
> * Source de données : stockage NAS (Network-Attached Storage)
> * Itinéraire de migration : NAS &rArr; DataBox &rArr; Azure File Share &rArr; synchronisation avec Windows Server
> * Mise en cache de fichiers locaux : Oui, l’objectif final étant un déploiement Azure File Sync.

Si votre scénario est différent, consultez le [tableau des guides de migration](storage-files-migration-overview.md#migration-guides).

Azure File Sync fonctionne sur les emplacements DAS (Direct Attached Storage) et ne prend pas en charge la synchronisation avec les emplacements NAS (Network Attached Storage).
En fait, la migration de vos fichiers est nécessaire et cet article vous guide tout au long de la planification et de l’exécution de cette migration.

## <a name="migration-goals"></a>Objectifs de la migration

L’objectif est de déplacer les partages que vous avez sur votre appliance NAS vers un Windows Server. Utilisez ensuite Azure File Sync pour un déploiement de cloud hybride. Cette migration doit être effectuée de manière à garantir l’intégrité des données de production et la disponibilité pendant la migration. Garantir la disponibilité implique de garder les temps d’arrêt à un niveau minimal pour qu’ils respectent les fenêtres de maintenance habituelles ou ne les dépassent que légèrement.

## <a name="migration-overview"></a>Vue d’ensemble de la migration

Le processus de migration se compose de plusieurs phases : Vous devez déployer des comptes de stockage Azure et des partages de fichiers, déployer un serveur Windows local, configurer Azure File Sync, migrer à l’aide de RoboCopy, et enfin, effectuer le basculement. Les sections suivantes décrivent en détail les phases du processus de migration.

> [!TIP]
> Si vous revenez à cet article, utilisez la navigation de droite pour accéder à la phase de migration là où vous vous étiez arrêté.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Phase 1 : Identifier le nombre de partages de fichiers Azure dont vous avez besoin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2 : Déployer des ressources de stockage Azure

Au cours de cette phase, consultez la table de mappage de la phase 1 et utilisez-la pour approvisionner le nombre correct de comptes de stockage Azure et de partages de fichiers dans ceux-ci.

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

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Phase 4 : Approvisionner un Windows Server local approprié

En attendant de recevoir vos Azure DataBox, vous pouvez commencer à examiner les besoins d’un ou plusieurs serveurs Windows que vous utiliserez avec Azure File Sync.

* Créez un serveur Windows Server 2019 (ou au minimum 2012R2) comme machine virtuelle ou serveur physique. Un cluster de basculement Windows Server est également pris en charge.
* Provisionnez ou ajoutez un stockage DAS (Direct Attached Storage). Notez que les dispositifs de stockage NAS ne sont pas pris en charge.

La configuration des ressources (calcul et RAM) du Windows Server que vous déployez dépend principalement du nombre d’éléments (fichiers et dossiers) que vous allez synchroniser. Nous vous recommandons d’utiliser une configuration haute performance si vous rencontrez des problèmes.

[Découvrez comment dimensionner un serveur Windows Server en fonction du nombre d’éléments (fichiers et dossiers) que vous devez synchroniser.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> L’article précédemment lié présente un tableau avec une plage pour la mémoire du serveur (RAM). Vous pouvez vous orienter vers le plus petit nombre de votre serveur, mais attendez-vous à ce que la synchronisation initiale prenne beaucoup plus de temps.

## <a name="phase-5-copy-files-onto-your-databox"></a>Phase 5 : Copier des fichiers sur votre DataBox

Une fois votre DataBox reçue, vous devez la configurer dans la ligne de mire de votre appliance NAS. Suivez la documentation relative au type de DataBox que vous avez commandé.

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

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Phase 6 : Déployer la ressource cloud Azure File Sync

Avant de poursuivre, attendez que tous vos fichiers soient arrivés dans les partages de fichiers Azure qui conviennent. Le processus de transmission et de réception des données DataBox prend du temps.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Phase 7 : Déployer l’agent Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Phase 8 : Configurer Azure File Sync sur Windows Server

Votre serveur Windows Server local inscrit doit être prêt et connecté à Internet pour ce processus.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Activez la fonctionnalité de hiérarchisation cloud et sélectionnez « Espace de noms uniquement » dans la section de téléchargement initial.

> [!IMPORTANT]
> La hiérarchisation cloud désigne la fonctionnalité AFS qui permet au serveur local d’avoir moins de capacité de stockage que ce qui est stocké dans le cloud, tout en ayant l’espace de noms complet disponible. Les données intéressantes localement sont également mises en cache localement pour des performances d’accès rapides. La hiérarchisation cloud est une fonctionnalité facultative par « point de terminaison de serveur » Azure File Sync. Vous devez utiliser cette fonctionnalité si vous ne disposez pas de suffisamment de capacité de disque local sur Windows Server pour conserver toutes les données cloud et si vous souhaitez éviter de télécharger toutes les données à partir du cloud.

Suivez de nouveau les étapes permettant de créer un groupe de synchronisation et d’ajouter le dossier serveur correspondant comme point de terminaison de serveur pour tous les partages de fichiers/emplacements de serveur Azure à configurer pour la synchronisation. Attendez que la synchronisation de l’espace de noms soit terminée. La section suivante vous explique comment vous en assurer.

> [!NOTE]
> Après la création d’un point de terminaison de serveur, la synchronisation fonctionne. Toutefois, la synchronisation doit énumérer (découvrir) les fichiers et dossiers que vous avez déplacés via DataBox dans le partage de fichiers Azure. Selon la taille de l’espace de noms, il peut s’écouler beaucoup de temps avant que l’espace de noms du cloud n’apparaisse sur le serveur.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Phase 9 : Attendre que l’espace de noms apparaisse complètement sur le serveur

Vous devez impérativement attendre que le serveur ait entièrement téléchargé l’espace de noms à partir du partage cloud. Si vous commencez à déplacer des fichiers trop tôt sur le serveur, vous risquez de vous heurter à des téléchargements inutiles et de provoquer des conflits de synchronisation de fichiers.

Pour savoir si votre serveur a terminé la synchronisation du téléchargement initial, ouvrez observateur d’événements sur votre serveur Windows Server en cours de synchronisation et utilisez le journal des événements de télémétrie Azure File Sync.
Le journal des événements de télémétrie se trouve dans l'observateur d'événements, sous Applications and Services\Microsoft\FileSync\Agent.

Recherchez l’événement 9102 le plus récent. L’ID d’événement 9102 est enregistré une fois que la session de terminaison se termine. Dans le texte de l’événement, il s’agit d’un champ correspondant à la direction de synchronisation du téléchargement. ( `HResult` doit être égal à zéro et les fichiers doivent également être téléchargés).
 
Il vous faut voir deux événements consécutifs de ce type et de ce contenu pour savoir que le serveur a terminé le téléchargement de l’espace de noms. Des événements distincts peuvent se déclencher entre deux événements 9102.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Phase 10 : Mettre à jour RoboCopy à partir de votre NAS

Lorsque votre serveur a terminé la synchronisation initiale de l’espace de noms entier à partir du partage cloud, vous pouvez passer à cette étape. Avant de poursuivre, il est impératif que cette étape soit terminée. Pour plus d’informations, consultez la section précédente.

Dans cette étape, vous allez exécuter des travaux RoboCopy pour intercepter vos partages cloud avec les dernières modifications apportées à votre NAS depuis la duplication de vos partages sur la DataBox.
Cette mise à jour RoboCopy peut se terminer rapidement ou prendre un certain temps selon le volume de modifications intervenues sur vos partages NAS.

> [!WARNING]
> En raison d’un comportement de régression de RoboCopy dans Windows Server 2019, le commutateur /MIR de RoboCopy n’est pas compatible avec un répertoire cible à plusieurs niveaux. Vous ne devez pas utiliser le client Windows Server 2019 ou Windows 10 pour cette phase de la migration. Utilisez RoboCopy sur un serveur Windows Server 2016 intermédiaire.

L’approche de migration de base consiste à effectuer une copie à partir de l’appliance NAS vers le serveur Windows Server à l’aide de RoboCopy et à effectuer une synchronisation avec des partages de fichiers Azure à l’aide d’Azure File Sync.

Exécutez la première copie locale vers votre dossier Windows Server cible :

1. Identifiez le premier emplacement sur votre appliance NAS.
1. Identifiez le dossier correspondant sur le serveur Windows Server sur lequel Azure File Sync est déjà configuré.
1. Démarrer la copie à l’aide de RoboCopy

La commande RoboCopy suivante copie uniquement les différences (fichiers et dossiers mis à jour) de votre stockage NAS vers votre dossier cible Windows Server. Le serveur Windows Server le synchronise ensuite avec le(s) partage(s) de fichiers Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Si vous avez configuré moins de stockage sur votre Windows Server que ce que vos fichiers occupent sur l’appliance NAS, cela signifie que vous avez configuré la hiérarchisation cloud. Quand le volume Windows Server local se remplit, la [hiérarchisation cloud](storage-sync-cloud-tiering-overview.md) intervient et hiérarchise les fichiers qui ont déjà été correctement synchronisés. La hiérarchisation cloud génère suffisamment d’espace pour poursuivre la copie à partir de l’appliance NAS. La hiérarchisation cloud effectue une vérification toutes les heures pour déterminer ce qui a été synchronisé et libérer de l’espace disque pour atteindre l’espace de volume libre de 99 %.
Il est possible que RoboCopy doive déplacer de nombreux fichiers, plus que le stockage local pour dont vous disposez sur le serveur Windows Server. En moyenne, vous pouvez vous attendre à ce que RoboCopy déplace beaucoup d’éléments plus rapidement qu’Azure File Sync charge vos fichiers et les hiérarchise sur votre volume local. RoboCopy échoue. Il est recommandé de traiter les partages dans une séquence qui empêche cela. Par exemple, ne pas démarrer les travaux RoboCopy pour tous les partages en même temps ou déplacer uniquement les partages qui correspondent à la quantité actuelle d’espace libre sur le Windows Server, pour n’en citer que quelques-uns. Le point positif réside dans le fait que le commutateur /MIR déplace uniquement les deltas et qu’une fois un delta déplacé, un travail redémarré n’a pas besoin de déplacer à nouveau ce fichier.

### <a name="user-cut-over"></a>Transfert de l’utilisateur

Quand vous exécutez la commande RoboCopy pour la première fois, les utilisateurs et applications ont toujours accès aux fichiers sur l’appliance NAS et peuvent éventuellement les modifier. Il est possible que RoboCopy traite un répertoire, passe au répertoire suivant, puis qu’un utilisateur accédant à l’emplacement source (NAS) ajoute, modifie ou supprime un fichier qui ne sera pas traité durant cette exécution de RoboCopy. Il s’agit du comportement attendu.

La première exécution consiste à déplacer la majeure partie des données évolutives vers votre serveur Windows Server et dans le cloud à l’aide d’Azure File Sync. Cette première copie peut prendre beaucoup de temps selon les paramètres suivants :

* La bande passante de chargement
* La vitesse du réseau local, ainsi que le nombre de threads RoboCopy qui y correspondent de manière optimale
* Le nombre d’éléments (fichiers et dossiers) qui doivent être traités par RoboCopy et Azure File Sync

Une fois l’exécution initiale terminée, réexécutez la commande.

Elle se termine plus rapidement la deuxième fois que vous exécutez RoboCopy pour le même partage. En effet, elle doit déplacer uniquement les éléments modifiés depuis la dernière exécution. Vous pouvez exécuter des travaux répétés pour le même partage.

Dès lors que vous considérez que le temps d’arrêt est acceptable, vous devez supprimer l’accès utilisateur à vos partages NAS. Pour ce faire, vous pouvez utiliser n’importe quelle étape empêchant les utilisateurs de modifier la structure des fichiers et des dossiers, ainsi que leur contenu. Par exemple, vous pouvez faire pointer votre DFS-Namespace vers un emplacement non existant ou modifier les listes de contrôle d’accès racine sur le partage.

Exécutez une dernière fois la commande RoboCopy pour traiter toutes les modifications qui n’ont pas encore été prises en compte.
La durée de cette dernière étape dépend de la vitesse d’analyse de RoboCopy. Vous pouvez estimer la durée d’exécution (correspondant au temps d’arrêt) en mesurant la durée de l’exécution précédente.

Créez un partage dans le dossier Windows Server et, le cas échéant, ajustez votre déploiement DFS-N pour qu’il pointe vers celui-ci. Veillez à définir les mêmes autorisations au niveau du partage que celles de votre partage SMB NAS. Si vous aviez un NAS joint à un domaine d’entreprise, les SID de l’utilisateur correspondent automatiquement à mesure que la présence des utilisateurs est vérifiée dans Active Directory et que RoboCopy copie fidèlement les fichiers et les métadonnées. Si vous avez utilisé des utilisateurs locaux sur votre emplacement NAS, vous devez recréer ces utilisateurs en tant qu’utilisateurs locaux de Windows Server et mapper les SID existants que RoboCopy a déplacés sur votre Windows Server avec les SID de vos nouveaux utilisateurs locaux de Windows Server.

Vous avez terminé la migration d’un partage/groupe de partages vers une racine ou un volume commun (selon votre mappage à partir de la phase 1)

Vous pouvez essayer d’exécuter quelques-unes de ces copies en parallèle. Nous vous recommandons de traiter l’étendue d’un partage de fichiers Azure à la fois.

## <a name="troubleshoot"></a>Dépanner

Le problème que vous êtes le plus susceptible de rencontrer est un échec de la commande RoboCopy de type *« Volume plein »* côté Windows Server. Toutes les heures, la hiérarchisation cloud retire le contenu du disque Windows Server local, qui a été synchronisé. Son objectif est d’atteindre 99 % d’espace libre sur le volume.

Laissez la synchronisation s’effectuer et la hiérarchisation cloud libérer l’espace disque. Vous pouvez observer l’opération dans l’Explorateur de fichiers sur votre serveur Windows Server.

Quand la capacité disponible de votre serveur Windows Server est suffisante, réexécutez la commande pour résoudre le problème. Cette situation n’entraîne aucune dégradation. Vous pouvez poursuivre en toute confiance. La nécessité de réexécuter la commande représente le seul inconvénient.

Pour plus d’informations sur la résolution des problèmes Azure File Sync, suivez le lien de la section suivante.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre davantage sur les partages de fichiers Azure et Azure File Sync. Les articles suivants vous aident à comprendre les options avancées et les meilleures pratiques et contiennent également des aides pour la résolution des problèmes. Ces articles sont liés à la [documentation relative aux partages de fichiers Azure](storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](storage-files-migration-overview.md)
* [Vue d’ensemble d’AFS](./storage-sync-files-planning.md)
* [Déployer AFS](./storage-how-to-create-file-share.md)
* [Résoudre les problèmes de synchronisation de fichiers Azure](storage-sync-files-troubleshoot.md)
