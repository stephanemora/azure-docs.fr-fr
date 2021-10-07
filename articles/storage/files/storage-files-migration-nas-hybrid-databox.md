---
title: Migration NAS locale vers Azure File Sync en utilisant Data Box
description: Découvrez comment migrer des fichiers d’un emplacement NAS (Network Attached Storage) vers un déploiement cloud hybride en utilisant Azure File Sync via Azure Data Box.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb7132e0a7b9dc59ac6b047d431acf0e740aba0a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430825"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>Utiliser Data Box pour migrer de Network Attached Storage (NAS) vers un déploiement cloud hybride avec Azure File Sync

Comme d’autres, cet article relatif à la migration comporte les mots clés NAS, Azure File Sync et Azure Data Box. Vérifiez qu’il s’applique à votre scénario :

> [!div class="checklist"]
> * Source de données : stockage NAS (Network-Attached Storage)
> * Route de migration : NAS &rArr; Data Box &rArr; Partage de fichiers Azure &rArr; synchronisation avec Windows Server
> * Mise en cache de fichiers localement : Oui, l’objectif final étant un déploiement Azure File Sync

Si votre scénario est différent, consultez le [tableau des guides de migration](storage-files-migration-overview.md#migration-guides).

Azure File Sync fonctionne sur les emplacements avec stockage en attachement direct (DAS, Direct Attached Storage). Il ne prend pas en charge les emplacements avec stockage en réseau (NAS, Network Attached Storage).
Vous devez donc migrer vos fichiers. Cet article vous guide tout au long de la planification et de l’implémentation de cette migration.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>Objectifs de la migration

L’objectif est de déplacer les partages que vous avez sur votre appliance NAS vers Windows Server. Vous utiliserez ensuite Azure File Sync pour un déploiement cloud hybride. Cette migration doit être effectuée de manière à garantir l’intégrité des données de production et la disponibilité pendant la migration. Garantir la disponibilité implique de garder les temps d’arrêt à un niveau minimal pour qu’ils respectent les fenêtres de maintenance habituelles ou ne les dépassent que légèrement.

## <a name="migration-overview"></a>Vue d’ensemble de la migration

Le processus de migration se compose de plusieurs phases : Plusieurs actions sont nécessaires :
- Déployer des comptes de stockage et des partages de fichiers Azure.
- Déployer un ordinateur local exécutant Windows Server. 
- Configurez Azure File Sync.
- Migrer des fichiers avec Robocopy.
- Effectuer le basculement.

Les sections suivantes décrivent en détail les phases du processus de migration.

> [!TIP]
> Si vous revenez à cet article, utilisez la navigation à droite de l’écran pour accéder à la phase de migration là où vous vous étiez arrêté.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Phase 1 : Déterminer le nombre de partages de fichiers Azure dont vous avez besoin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2 : Déployer des ressources de stockage Azure

Au cours de cette phase, consultez la table de mappage de la phase 1 et utilisez-la pour configurer le nombre correct de comptes de stockage Azure et de partages de fichiers dans ceux-ci.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>Phase 3 : Déterminer le nombre d’appliances Azure Data Box dont vous avez besoin

Démarrez cette étape uniquement après avoir terminé la phase précédente. À ce stade, vos ressources de stockage Azure (comptes de stockage et partages de fichiers) doivent être créées. Quand vous commandez votre Data Box, vous devez spécifier les comptes de stockage dans lesquels la Data Box déplace les données.

Au cours de cette phase, vous devez mapper les résultats du plan de migration de la phase précédente aux limites des options Data Box disponibles. Ces considérations vous aideront à établir un plan correspondant aux options Data Box que vous devez choisir et combien d’entre elles vous seront nécessaires pour déplacer vos partages NAS vers des partages de fichiers Azure.

Pour déterminer le nombre d’appareils dont vous avez besoin et leurs types, tenez compte des limites importantes suivantes :

* Une appliance Azure Data Box peut déplacer des données dans un maximum de 10 comptes de stockage. 
* Chaque option Data Box a sa propre capacité utilisable. Consultez [Options Data Box](#data-box-options).

Consultez votre plan de migration pour trouver le nombre de comptes de stockage que vous avez décidé de créer, ainsi que les partages dans chacun d’entre eux. Examinez ensuite la taille de chaque partage sur votre NAS. La combinaison de ces informations vous sera utile à des fins d’optimisation, ainsi que pour choisir appliance qui devra envoyer des données vers les comptes de stockage. Deux appareils Data Box peuvent déplacer des fichiers dans le même compte de stockage, mais ne répartissent pas le contenu d’un partage de fichiers unique sur deux Data Box.

### <a name="data-box-options"></a>Options Data Box

Pour une migration standard, choisissez une option ou une combinaison des options Data Box suivantes : 

* **Data Box Disk**.
  Microsoft vous enverra entre un et cinq disques SSD avec une capacité de 8 Tio chacun, pour un total maximal de 40 Tio. La capacité utilisable est environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez la [documentation de Data Box Disk](../../databox/data-box-disk-overview.md).
* **Data Box**.
  Cette option est la plus courante. Microsoft vous enverra une appliance Data Box renforcée qui fonctionne de façon similaire à un NAS. Elle dispose d’une capacité utilisable de 80 Tio. Pour plus d’informations, consultez la [documentationde Data Box](../../databox/data-box-overview.md).
* **Data Box Heavy**.
  Cette option comprend une appliance Data Box renforcée sur roues qui fonctionne de façon similaire à un NAS. Sa capacité est de 1 Pio. La capacité utilisable est environ 20 % inférieure en raison du chiffrement et de la surcharge du système de fichiers. Pour plus d’informations, consultez la [documentationde Data Box Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>Phase 4 : Provisionner une instance Windows Server locale appropriée

En attendant de recevoir vos appareils Azure Data Box, vous pouvez commencer à examiner les besoins d’une ou plusieurs instances Windows Server que vous utiliserez avec Azure File Sync.

* Créez une instance Windows Server 2019 (au minimum, Windows Server 2012 R2) comme machine virtuelle ou serveur physique. Un cluster de basculement Windows Server est également pris en charge.
* Provisionnez ou ajoutez un stockage en attachement direct (DAS, et non NAS qui n’est pas pris en charge).

La configuration des ressources (capacité de calcul et RAM) de l’instance Windows Server que vous déployez dépend principalement du nombre de fichiers et de dossiers que vous allez synchroniser. Nous vous recommandons une configuration haute performance si vous rencontrez des problèmes.

[Découvrez comment dimensionner une instance Windows Server en fonction du nombre de fichiers et de dossiers que vous devez synchroniser.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> L’article précédemment lié comprend un tableau avec une plage pour la mémoire du serveur (RAM). Vous pouvez utiliser des nombres dans la partie inférieure de la plage pour votre serveur, mais attendez-vous à ce que la synchronisation initiale prenne beaucoup plus de temps.

## <a name="phase-5-copy-files-onto-your-data-box"></a>Phase 5 : Copier des fichiers sur votre Data Box

Quand votre Data Box arrive, vous devez la configurer dans la ligne de mire de votre appliance NAS. Suivez la documentation d’installation pour le type de Data Box que vous avez commandé :

* [Configurer Data Box](../../databox/data-box-quickstart-portal.md)
* [Configurer Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Configurer Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Selon le type de Data Box, des outils de copie Data Box peuvent être disponibles. À ce stade, nous ne les recommandons pas pour des migrations vers des partages de fichiers Azure, car ils ne copient pas vos fichiers assez fidèlement sur la Data Box. Utilisez Robocopy à la place.

Quand votre Data Box arrive, elle dispose de partages SMB préprovisionnés pour chaque compte de stockage spécifié au moment de la commande.

* Si vos fichiers sont placés dans un partage de fichiers Azure Premium, vous disposerez d’un partage SMB par compte de stockage « stockage de fichiers » Premium.
* Si vos fichiers sont placés dans un compte de stockage standard, vous disposerez de trois partages SMB par compte de stockage standard (GPv1 et GPv2). Seuls les partages de fichiers se terminant par `_AzFiles` sont pertinents pour votre migration. Ignorez les partages d’objets blob de blocs et de pages.

Suivez les étapes décrites dans la documentation Azure Data Box :

1. [Connectez-vous à Data Box](../../databox/data-box-deploy-copy-data.md).
1. Copiez des données sur Data Box.
1. [Préparez votre Data Box pour le chargement sur Azure](../../databox/data-box-deploy-picked-up.md).

La documentation Data Box connexe spécifie une commande Robocopy. Cette commande ne convient pas pour préserver les fichiers et les dossiers avec fidélité. Utilisez plutôt la commande suivante :

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Phase 6 : Déployer la ressource cloud Azure File Sync

Avant de poursuivre, attendez que tous vos fichiers soient arrivés dans les partages de fichiers Azure qui conviennent. Le processus de transmission et d’ingestion des données Data Box prend du temps.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Phase 7 : Déployer l’agent Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>Phase 8 : Configurer Azure File Sync sur l’instance Windows Server

Votre instance Windows Server locale inscrite doit être prête et connectée à Internet pour ce processus.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Activez la fonctionnalité de hiérarchisation cloud et sélectionnez **Espace de noms uniquement** dans la section de téléchargement initial.

> [!IMPORTANT]
> La hiérarchisation cloud désigne la fonctionnalité Azure File Sync qui permet au serveur local d’avoir moins de capacité de stockage que ce qui est stocké dans le cloud, tout en ayant l’espace de noms complet disponible. Les données intéressantes localement sont également mises en cache localement pour des performances d’accès rapides. La hiérarchisation cloud est facultative. Vous pouvez la définir individuellement pour chaque point de terminaison de serveur Azure File Sync. Vous devez utiliser cette fonctionnalité si vous ne disposez pas de suffisamment de capacité de disque local sur l’instance Windows Server pour conserver toutes les données cloud et si vous souhaitez éviter de télécharger toutes les données à partir du cloud.

Pour tous les partages de fichiers/emplacements de serveur Azure à configurer pour la synchronisation, effectuez de nouveau les étapes permettant de créer des groupes de synchronisation et d’ajouter les dossiers serveur correspondants comme points de terminaison de serveur. Attendez que la synchronisation de l’espace de noms soit terminée. La section suivante explique comment vérifier que la synchronisation est terminée.

> [!NOTE]
> Après la création d’un point de terminaison de serveur, la synchronisation fonctionne. Toutefois, la synchronisation doit énumérer (découvrir) les fichiers et dossiers que vous avez déplacés par le biais de Data Box dans le partage de fichiers Azure. Selon la taille de l’espace de noms, il peut s’écouler beaucoup de temps avant que l’espace de noms du cloud n’apparaisse sur le serveur.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Phase 9 : Attendre que l’espace de noms apparaisse complètement sur le serveur

Avant de passer aux étapes suivantes de votre migration, attendez que le serveur ait entièrement téléchargé l’espace de noms à partir du partage cloud. Si vous commencez à déplacer des fichiers trop tôt sur le serveur, vous risquez de vous heurter à des téléchargements inutiles voire de provoquer des conflits de synchronisation de fichiers.

Pour déterminer si votre serveur a terminé la synchronisation du téléchargement initial, ouvrez l’observateur d’événements sur votre instance Windows Server en cours de synchronisation et utilisez le journal des événements de télémétrie Azure File Sync.
Le journal des événements de télémétrie se trouve dans l’observateur d’événements, sous Applications and Services\Microsoft\FileSync\Agent.

Recherchez l’événement 9102 le plus récent. L’ID d’événement 9102 est enregistré une fois que la session de terminaison se termine. Dans le texte de l’événement, un champ correspond à la direction de synchronisation du téléchargement. (`HResult` doit être égal à zéro et les fichiers doivent être téléchargés.)
 
Vous devez voir deux événements consécutifs de ce type, avec ce contenu, pour savoir que le serveur a terminé le téléchargement de l’espace de noms. Il est possible que d’autres événements se trouvent entre les deux événements 9102.

## <a name="phase-10-run-robocopy-from-your-nas"></a>Phase 10 : Exécuter Robocopy à partir de votre NAS

Une fois que votre serveur a terminé la synchronisation initiale de l’espace de noms entier à partir du partage cloud, vous pouvez passer à cette étape. Vous devez vérifier que la synchronisation initiale est terminée avant de passer à cette étape. Pour plus d’informations, consultez la section précédente.

Dans cette étape, vous allez exécuter des travaux Robocopy pour synchroniser vos partages cloud avec les dernières modifications apportées à votre NAS depuis la duplication de vos partages sur la Data Box.
Cette exécution de Robocopy peut se terminer rapidement ou prendre un certain temps selon le volume de modifications intervenues sur vos partages NAS.

> [!WARNING]
> En raison de la régression du comportement Robocopy dans Windows Server 2019, le commutateur Robocopy `/MIR` n’est pas compatible avec les répertoires cibles hiérarchisés. Vous ne pouvez pas utiliser le client Windows Server 2019 ou Windows 10 pour cette phase de la migration. Utilisez Robocopy sur une instance Windows Server 2016 intermédiaire.

Voici l’approche de la migration de base :
 - Exécutez Robocopy à partir de votre appliance NAS pour synchroniser votre instance Windows Server. 
 - Utilisez Azure File Sync pour synchroniser les partages de fichiers Azure à partir de Windows Server.

Exécutez la première copie locale vers votre dossier Windows Server cible :

1. Identifiez le premier emplacement sur votre appliance NAS.
1. Identifiez le dossier correspondant sur l’instance Windows Server où Azure File Sync est déjà configuré.
1. Démarrez la copie à l’aide de Robocopy.

La commande Robocopy suivante copie uniquement les différences (fichiers et dossiers mis à jour) de votre stockage NAS vers votre dossier cible Windows Server. L’instance Windows Server les synchronise ensuite avec les partages de fichiers Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Si vous avez provisionné moins de stockage sur votre instance Windows Server que ce que vos fichiers utilisent sur l’appliance NAS, vous avez configuré la hiérarchisation cloud. À mesure que le volume Windows Server local se remplit, la [hiérarchisation cloud](../file-sync/file-sync-cloud-tiering-overview.md) intervient et hiérarchise les fichiers qui ont déjà été correctement synchronisés. La hiérarchisation cloud génère suffisamment d’espace pour poursuivre la copie à partir de l’appliance NAS. La hiérarchisation cloud effectue une vérification toutes les heures pour déterminer ce qui a été synchronisé et libérer de l’espace disque pour atteindre l’espace de volume libre de 99 %.

Robocopy devra peut-être déplacer plus de fichiers que vous ne pouvez en stocker localement sur l’instance Windows Server. Vous pouvez vous attendre à ce que Robocopy déplace les fichiers plus rapidement qu’Azure File Sync, lequel ne peut pas les charger et les hiérarchiser à temps sur votre volume local. Dans ce cas, Robocopy échoue. Nous vous recommandons de traiter les partages dans une séquence qui permet d’éviter ce scénario. Par exemple, déplacez uniquement les partages qui tiennent dans l’espace libre disponible sur l’instance Windows Server. Ou évitez de démarrer des tâches Robocopy pour tous les partages en même temps. La bonne nouvelle, c’est que le commutateur `/MIR` garantit que seuls les deltas sont déplacés. Une fois qu’un delta a été déplacé, un travail redémarré n’a plus besoin de déplacer à nouveau le fichier.

### <a name="do-the-cutover"></a>Effectuer le basculement

Quand vous exécutez la commande Robocopy pour la première fois, les utilisateurs et applications ont toujours accès aux fichiers sur l’appliance NAS et peuvent éventuellement les modifier. Robocopy traite un répertoire, puis passe au suivant. Un utilisateur sur le NAS peut ensuite ajouter, modifier ou supprimer un fichier sur le premier répertoire qui ne sera pas traité pendant l’exécution actuelle de Robocopy. Il s’agit du comportement attendu.

La première exécution consiste à déplacer la majeure partie des données traitées vers votre instance Windows Server et dans le cloud à l’aide d’Azure File Sync. Cette première copie peut prendre beaucoup de temps selon les paramètres suivants :

* La bande passante de chargement.
* La vitesse du réseau local, ainsi que le nombre de threads Robocopy qui y correspondent de manière optimale.
* Le nombre d’éléments (fichiers et dossiers) qui doivent être traités par Robocopy et Azure File Sync.

Une fois l’exécution initiale terminée, réexécutez la commande.

Robocopy se termine plus rapidement la deuxième fois que vous l’exécutez pour un partage. En effet, seules les modifications effectuées depuis la dernière exécution doivent être déplacées. Vous pouvez exécuter des travaux répétés pour le même partage.

Dès lors que vous considérez que le temps d’arrêt est acceptable, vous devez supprimer l’accès utilisateur à vos partages NAS. Pour ce faire, vous pouvez utiliser n’importe quelle méthode empêchant les utilisateurs de modifier la structure des fichiers et des dossiers, ainsi que leur contenu. Par exemple, vous pouvez pointer votre espace de noms DFS vers un emplacement qui n’existe pas ou changer les ACL racine sur le partage.

Exécutez Robocopy une dernière fois pour traiter toutes les modifications qui n’ont pas encore été prises en compte.
La durée de cette dernière étape dépend de la vitesse d’analyse de Robocopy. Vous pouvez estimer la durée d’exécution (correspondant au temps d’arrêt) en mesurant la durée de l’exécution précédente.

Créez un partage dans le dossier Windows Server et, le cas échéant, ajustez votre déploiement DFS-N pour qu’il pointe vers celui-ci. Veillez à définir les mêmes autorisations au niveau du partage que celles de votre partage SMB NAS. Si vous aviez un NAS joint à un domaine d’entreprise, les SID de l’utilisateur correspondent automatiquement car les utilisateurs sont dans Active Directory et Robocopy copie fidèlement les fichiers et les métadonnées. Si vous avez utilisé des utilisateurs locaux sur votre NAS, vous devez : 
- Recréer ces utilisateurs en tant qu’utilisateurs locaux de Windows Server. 
- Mapper les SID existants déplacés par Robocopy vers votre instance Windows Server aux SID de vos nouveaux utilisateurs locaux de Windows Server.

Vous avez terminé la migration d’un partage ou d’un groupe de partages vers une racine ou un volume commun (selon votre mappage dans la phase 1).

Vous pouvez essayer d’exécuter quelques-unes de ces copies en parallèle. Nous vous recommandons de traiter l’étendue d’un partage de fichiers Azure à la fois.

## <a name="deprecated-option-offline-data-transfer"></a>Option obsolète : « transfert de données hors connexion »

Avant la sortie de la version 13 de l’agent Azure File Sync, l’intégration de Data Box était réalisée par un processus appelé « transfert de données hors connexion ». Ce processus est devenu obsolète. Avec la version 13 de l’agent, il a été remplacé par la procédure bien plus simple et rapide décrite dans cet article. Cependant, si vous le souhaitez, vous pouvez toujours utiliser la fonctionnalité « transfert de données hors connexion » obsolète. Elle est toujours disponible en utilisant un [module PowerShell AFS plus ancien](https://www.powershellgallery.com/packages/Az.StorageSync/1.4.0) spécifique:

```powershell
Install-Module Az.StorageSync -RequiredVersion 1.4.0
Import-module Az.StorageSync -RequiredVersion 1.4.0
# Verify the specific version is loaded:
Get-module Az.StorageSync
```
Vous pouvez ensuite continuer à créer un point de terminaison de serveur à l’aide du même module PowerShell et spécifier un partage de mise en lots dans le processus.
Si vous avez une migration en cours avec le processus de transfert de données hors connexion, votre migration se poursuivra comme prévu, et vous devrez toujours désactiver ce paramètre une fois la migration terminée.
La possibilité de démarrer de nouvelles migrations avec ce processus obsolète sera supprimée lors d’une prochaine version de l’agent.

## <a name="troubleshooting"></a>Résolution des problèmes

Le problème le plus courant est un échec de la commande Robocopy de type « Volume plein » côté Windows Server. Toutes les heures, la hiérarchisation cloud retire le contenu du disque Windows Server local qui a été synchronisé. Son objectif est d’atteindre 99 % d’espace libre sur le volume.

Laissez la synchronisation s’effectuer et la hiérarchisation cloud libérer l’espace disque. Vous pouvez observer l’opération dans l’Explorateur de fichiers sur votre instance Windows Server.

Quand la capacité disponible de votre instance Windows Server est suffisante, réexécutez la commande pour résoudre le problème. Tout fonctionne dans cette situation. Vous pouvez continuer en toute confiance. La nécessité de réexécuter la commande représente le seul inconvénient.

Pour résoudre les problèmes liés à Azure File Sync, consultez l’article indiqué dans la section suivante.

## <a name="next-steps"></a>Étapes suivantes

Il y a bien d’autres choses à apprendre sur les partages de fichiers Azure et Azure File Sync. Les articles suivants vous aideront à comprendre les options avancées et les bonnes pratiques. Vous verrez également comment résoudre les problèmes. Ces articles contiennent des liens vers la [documentation sur les partages de fichiers Azure](storage-files-introduction.md), le cas échéant.

* [Vue d’ensemble de la migration](storage-files-migration-overview.md)
* [Planification d’un déploiement de synchronisation de fichiers Azure](../file-sync/file-sync-planning.md)
* [Créer un partage de fichiers](storage-how-to-create-file-share.md)
* [Résoudre les problèmes d’Azure File Sync](../file-sync/file-sync-troubleshoot.md)