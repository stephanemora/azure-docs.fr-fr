---
title: Migrer les données vers Azure File Sync à l’aide d’Azure Data Box et autres méthodes
description: Migrer des données en bloc d’une manière qui est compatible avec Azure File Sync.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 04b13c1e511f54c1fcf7b632d3a368fde16bf319
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549011"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrer des données en bloc vers Azure File Sync
Vous pouvez migrer des données en bloc vers Azure File Sync de deux manières :

* **Charger vos fichiers à l’aide d’Azure File Sync.** Il s’agit de la méthode la plus simple. Déplacer les fichiers localement vers Windows Server 2012 R2 ou version ultérieure et installez l’agent Azure File Sync. Après avoir configuré la synchronisation, vos fichiers seront téléchargés à partir du serveur. (Nos clients expérience actuellement une vitesse moyenne de 1 TIO sur tous les deux jours). Pour vous assurer que votre serveur n’utilise pas trop de la bande passante pour votre centre de données, vous souhaiterez peut-être configurer un [planification de limitation de bande passante](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transférer vos fichiers hors connexion.** Si vous n’avez pas suffisamment de bande passante, vous n’êtes peut-être pas en mesure de télécharger des fichiers vers Azure dans un délai raisonnable. Le défi consiste à la synchronisation initiale de l’ensemble de fichiers. Pour surmonter ce défi, utiliser les outils de migration hors connexion en bloc telles que la [famille d’Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Cet article explique comment migrer des fichiers hors connexion d’une manière qui est compatible avec Azure File Sync. Suivez ces instructions pour éviter les conflits de fichier et de conserver vos fichiers et les listes de contrôle d’accès (ACL) dossier et les horodatages après avoir activé la synchronisation.

## <a name="online-migration-tools"></a>Outils de migration en ligne
Le processus que nous décrivons dans cet article ne fonctionne non seulement pour la zone de données, mais aussi pour d’autres outils de migration hors connexion. Elle fonctionne également pour les outils en ligne tels qu’AzCopy, Robocopy, ou outils partenaires et services. Toutefois vous surmonter initial télécharger défi, suivez les étapes décrites dans cet article pour utiliser ces outils d’une manière compatible avec Azure File Sync.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Avantages de l’utilisation d’un outil pour transférer des données hors connexion
Voici les principaux avantages de l’utilisation d’un outil de transfert comme zone de données pour la migration hors connexion :

- Vous n’êtes pas obligé de télécharger tous vos fichiers sur le réseau. Pour les grands espaces de noms, cet outil peut économiser du temps et bande passante importante.
- Lorsque vous utilisez Azure File Sync, peu importe quel outil de transfert, vous utilisez (zone de données, service Azure Import/Export et ainsi de suite), votre serveur de production télécharge uniquement les fichiers modifiés après avoir déplacé les données vers Azure.
- Azure File Sync synchronise vos ACL du fichier et dossier même si l’outil de migration hors connexion ne transport ACL.
- Data Box et Azure File Sync ne nécessitent aucun arrêt. Lorsque vous utilisez Data Box pour transférer des données dans Azure, la bande passante réseau efficacement et de préserver la fidélité du fichier. Vous également garder votre espace de noms à jour en téléchargeant uniquement les fichiers modifiés après avoir déplacé les données vers Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Conditions préalables pour le transfert de données hors connexion
Vous ne devez pas activer la synchronisation sur le serveur que vous effectuez une migration avant d’effectuer le transfert de données hors connexion. Autres choses à prendre en compte avant de commencer sont les suivantes :

- Si vous envisagez d’utiliser Data Box pour votre migration en bloc : Passez en revue les [prérequis de déploiement pour Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planifiez votre topologie Azure File Sync finale : [Planifier un déploiement de la fonctionnalité Synchronisation de fichiers Azure](storage-sync-files-planning.md)
- Sélectionnez le ou les comptes de stockage Azure destinés à contenir les partages de fichiers avec lesquels vous souhaitez vous synchroniser. Veillez à effectuer votre migration en bloc dans des partages intermédiaires temporaires situés dans les mêmes comptes de stockage. La migration en bloc ne peut être activée qu’en utilisant un partage final (et une partage intermédiaire) résidant dans le même compte de stockage.
- Une migration en bloc ne peut être utilisée que si vous créez une nouvelle relation de synchronisation avec un emplacement serveur. Vous ne pouvez pas activer une migration en bloc avec une relation de synchronisation existante.


## <a name="process-for-offline-data-transfer"></a>Processus de transfert de données hors connexion
Voici comment configurer la synchronisation de fichiers Azure de manière compatible avec les outils de migration en bloc telles que Azure Data Box :

![Diagramme montrant comment configurer la synchronisation de fichiers Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Étape | Détails |
|---|---------------------------------------------------------------------------------------|
| ![Étape 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Commandez votre Data Box](../../databox/data-box-deploy-ordered.md). Les offres de famille Data Box [plusieurs produits](https://azure.microsoft.com/services/storage/databox/data) à vos besoins. Lorsque vous recevez votre Data Box, suivez son [documentation pour copier vos données](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) à ce chemin d’accès UNC sur la zone de données :  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<Nom_partage\>*. Ici, *nom_partage* est le nom du partage intermédiaire. Renvoyez la Data Box à Azure. |
| ![Étape 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendez que vos fichiers s’affichent dans les partages de fichiers Azure que vous avez choisi en tant que partages intermédiaires temporaires. *N’activez pas la synchronisation à ces partages.* |
| ![Étape 3 :](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Créer un nouveau partage vide pour chaque partage de fichiers Data Box est créé pour vous. Ce nouveau partage doit être dans le même compte de stockage que le partage de la zone de données. [Guide pratique pour créer un partage de fichiers Azure](storage-how-to-create-file-share.md). |
| ![Étape 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Créer un groupe de synchronisation](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) dans un service de synchronisation de stockage. Référencer le partage vide en tant que point de terminaison cloud. Répétez cette étape pour chaque partage de fichiers Data Box. [Configurer Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Étape 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Ajoutez le répertoire de votre serveur en ligne comme point de terminaison de serveur](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Dans le processus, spécifiez que vous avez déplacé les fichiers vers Azure et référencer les partages de mise en lots. Vous pouvez activer ou désactiver la hiérarchisation en fonction des besoins cloud. Lors de la création d’un point de terminaison de serveur sur votre serveur en direct, faire référence au partage de mise en lots. Sur le **ajouter le point de terminaison de serveur** panneau, sous **hors connexion de transfert de données**, sélectionnez **activé**, puis sélectionnez le partage intermédiaire qui doit se trouver dans le même compte de stockage que le cloud point de terminaison. Ici, la liste des partages disponibles est filtrée par compte de stockage et les partages qui ne sont pas déjà une synchronisation. |

![Capture d’écran de l’interface utilisateur du portail Azure, montrant comment activer le transfert de données hors connexion lors de la création d’un nouveau point de terminaison de serveur](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronisation du partage
Après avoir créé votre point de terminaison de serveur, la synchronisation démarre. Le processus de synchronisation détermine si chaque fichier sur le serveur existe également dans le partage intermédiaire où la zone de données déposée les fichiers. Si le fichier existe, le processus de synchronisation copie le fichier depuis le partage intermédiaire au lieu de télécharger à partir du serveur. Si le fichier n’existe pas dans le partage de mise en lots, ou si une version plus récente est disponible sur le serveur local, le processus de synchronisation télécharge le fichier à partir du serveur local.

> [!IMPORTANT]
> Vous pouvez activer le mode de migration en bloc uniquement lorsque vous créez un point de terminaison de serveur. Après avoir établi un point de terminaison de serveur, vous ne pouvez pas intégrer migrés en bloc des données d’un serveur de synchronisation déjà dans l’espace de noms.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listes ACL et horodatages des fichiers et dossiers
Azure File Sync permet de s’assurer que les ACL de fichiers et dossiers est synchronisés à partir du serveur en direct même si l’outil de migration que vous avez utilisé n’a pas de transport initialement ACL. Pour cette raison, le partage de zone de transit n’a pas besoin de contenir toutes les ACL des fichiers et dossiers. Lorsque vous activez la fonctionnalité de migration de données hors connexion lorsque vous créez un nouveau point de terminaison de serveur, toutes les ACL du fichier sont synchronisées sur le serveur. Les horodateurs qui vient d’être créées et modifiées sont également synchronisées.

## <a name="shape-of-the-namespace"></a>Forme de l’espace de noms
Lorsque vous activez la synchronisation, le contenu du serveur détermine la forme de l’espace de noms. Si les fichiers sont supprimés du serveur local une fois l’instantané de la zone de données et la migration, ne déplacement pas ces fichiers dans l’espace de noms en direct, la synchronisation. Ils restent dans le partage de zone de transit, mais ils ne sont pas copiés. Cela est nécessaire, car la synchronisation conserve l’espace de noms en fonction du serveur de production. La zone de données *instantané* est simplement une base intermédiaire pour la copie de fichier efficace. Il n’est pas l’autorité pour la forme de l’espace de noms en direct.

## <a name="cleaning-up-after-bulk-migration"></a>Nettoyage après la migration d’en bloc 
Comme le serveur a terminé sa synchronisation initiale de l’espace de noms, les fichiers de migrés en bloc de zone de données utilisent le partage de fichiers intermédiaire. Sur le **propriétés du point de terminaison serveur** panneau dans le portail Azure, dans le **hors connexion de transfert de données** section, l’état passe de **en cours** à **terminé** . 

![Capture d’écran du panneau Propriétés de point de terminaison de serveur, dans lequel se trouvent les contrôles d’état et les désactiver pour le transfert de données hors connexion](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Vous pouvez désormais nettoyer le partage de mise en lots pour réduire les coûts :

1. Sur le **propriétés du point de terminaison serveur** panneau, lorsque l’état est **terminé**, sélectionnez **désactiver le transfert de données hors connexion**.
2. Envisagez de supprimer le partage de mise en lots pour réduire les coûts. Le partage de mise en lots ne contient probablement ACL du fichier et dossier, ce qui n’est pas très utile. Fins de sauvegarde point-à-temps, créez une véritable [instantané de partage de fichiers Azure synchronisation](storage-snapshots-files.md). Vous pouvez [configurer sauvegarde Azure pour prendre des instantanés]( ../../backup/backup-azure-files.md) selon une planification.

Désactiver le mode de transfert de données hors connexion uniquement lorsque l’état est **terminé** ou lorsque vous souhaitez annuler en raison d’une configuration incorrecte. Si vous désactivez le mode lors d’un déploiement, fichiers commence à télécharger à partir du serveur, même si votre partage intermédiaire est toujours disponible.

> [!IMPORTANT]
> Une fois que vous désactivez le mode de transfert de données hors connexion, vous ne pouvez pas réactiver, même si le partage intermédiaire à partir de la migration en bloc est toujours disponible.

## <a name="next-steps"></a>Étapes suivantes
- [Planifier un déploiement de la fonctionnalité Synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
