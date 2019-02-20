---
title: Utilisez Data Box et d’autres méthodes pour l’ingestion en mode hors connexion dans Azure File Sync.
description: Processus et bonnes pratiques d’activation de la prise en charge de la migration en bloc compatible avec la synchronisation.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212444"
---
# <a name="migrate-to-azure-file-sync"></a>Migrer vers Azure File Sync
Il existe plusieurs options de déplacement vers Azure File Sync :

### <a name="uploading-files-via-azure-file-sync"></a>Chargement de fichiers via Azure File Sync
L’option la plus simple consiste à déplacer vos fichiers localement sur un serveur Windows Server version 2012 R2 ou ultérieure et à y installer l’agent Azure File Sync. Une fois la synchronisation configurée, vos fichiers se chargent à partir du serveur. Nous observons actuellement une vitesse de chargement moyenne parmi tous nos clients de 1 To sur tous les 2 jours.
Envisagez d’établir un [programme de limitation de bande passante](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) pour assurer le bon fonctionnement de votre serveur dans le centre de données.

### <a name="offline-bulk-transfer"></a>Transfert en bloc hors connexion
Si le chargement est certainement l’option la plus simple, il se peut qu’elle ne fonctionne pas dans votre cas si votre bande passante disponible ne vous permet pas de synchroniser vos fichiers vers Azure dans un délai raisonnable. La difficulté à surmonter ici est celle liée à la synchronisation initiale de l’ensemble des fichiers. Après quoi, Azure File Sync déplacera uniquement les modifications à mesure qu’elles se produiront dans l’espace de noms, ce qui demande généralement beaucoup moins de bande passante.
Pour surmonter la difficulté liée au chargement initial, vous pouvez utiliser des outils de migration en bloc hors connexion comme la [famille Azure Data Box](https://azure.microsoft.com/services/storage/databox). L’article suivant porte essentiellement sur le processus à suivre pour bénéficier d’une migration hors connexion compatible avec Azure File Sync. Il décrit les bonnes pratiques qui vous aideront à éviter les conflits de fichiers et à préserver les listes ACL et les horodatages de vos fichiers et dossiers une fois que vous aurez activé la synchronisation.

### <a name="online-migration-tools"></a>Outils de migration en ligne
Le processus décrit ci-dessous ne vaut pas seulement pour Data Box. Il convient pour n’importe quel outil de migration hors connexion (comme Data Box), les outils en ligne, comme AzCopy et Robocopy, ou les outils et services tiers. Donc, quelle que soit la méthode employée pour surmonter la difficulté liée au chargement initial, il importe quand même de suivre les étapes décrites ci-dessous pour utiliser ces outils de façon compatible avec la synchronisation.


## <a name="offline-data-transfer-benefits"></a>Avantages du transfert de données hors connexion
Les principaux avantages de la migration hors connexion avec Data Box sont les suivants :

- Quand il s’agit de migrer des fichiers dans Azure via un processus de transfert en bloc hors connexion, comme Data Box, il n’est pas nécessaire de charger tous les fichiers à partir du serveur en empruntant le réseau. Pour les espaces de noms volumineux, cela peut signifier des gains importants en matière de bande passante réseau et de temps.
- Quand vous utilisez Azure File Sync, quel que soit le mode de transport utilisé (Data Box, Azure Import, etc.), votre serveur en ligne charge uniquement les fichiers qui ont changé depuis votre expédition des données vers Azure.
- Azure File Sync vérifie que les listes ACL de vos fichiers et dossiers sont également synchronisées, même si le produit de migration en bloc hors connexion ne transporte pas les listes ACL.
- Quand vous utilisez Azure Data Box et Azure File Sync, il n’y a aucun temps d’arrêt. Avec Data Box, le transfert de données dans Azure optimise l’utilisation de la passante du réseau tout en préservant la fidélité des fichiers. Par ailleurs, l’outil tient à jour votre espace de noms en chargeant uniquement les fichiers qui ont changé depuis l’envoi de la Data Box.

## <a name="plan-your-offline-data-transfer"></a>Planifier votre transfert de données hors connexion
Avant de commencer, passez en revue les informations suivantes :

- Effectuez votre migration en bloc sur un ou plusieurs partages de fichiers Azure avant d’activer la synchronisation avec Azure File Sync.
- Si vous envisagez d’utiliser Data Box pour votre migration en bloc : Passez en revue les [prérequis de déploiement pour Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planification de votre topologie finale Azure File Sync : [Planifier un déploiement de la fonctionnalité Synchronisation de fichiers Azure](storage-sync-files-planning.md)
- Sélectionnez le ou les comptes de stockage Azure destinés à contenir les partages de fichiers avec lesquels vous souhaitez vous synchroniser. Veillez à effectuer votre migration en bloc dans des partages intermédiaires temporaires situés dans les mêmes comptes de stockage. La migration en bloc ne peut être activée qu’en utilisant un partage final (et une partage intermédiaire) résidant dans le même compte de stockage.
- Une migration en bloc ne peut être utilisée que si vous créez une nouvelle relation de synchronisation avec un emplacement serveur. Vous ne pouvez pas activer une migration en bloc avec une relation de synchronisation existante.

## <a name="offline-data-transfer-process"></a>Processus de transfert de données hors connexion
Cette section décrit le processus de configuration d’Azure File Sync d’une manière qui soit compatible avec les outils de migration en bloc comme Azure Data Box.

![Visualisation des étapes du processus qui sont également expliquées en détail dans un paragraphe suivant](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Étape | Détails |
|---|---------------------------------------------------------------------------------------|
| ![Étape 1 du processus](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Commandez votre Data Box](../../databox/data-box-deploy-ordered.md). Il existe [au sein de la famille Data Box plusieurs offres](https://azure.microsoft.com/services/storage/databox/data) destinées à répondre à vos besoins. Après avoir reçu votre Data Box, suivez les instructions fournies dans la [documentation de la Data Box pour copier vos données](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Veillez à ce que les données soient copiées sur ce chemin UNC de la Data Box : `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` où `ShareName` est le nom du partage intermédiaire. Renvoyez la Data Box à Azure. |
| ![Étape 2 du processus](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendez que vos fichiers s’affichent dans les partages de fichiers Azure que vous avez désignés comme partages intermédiaires temporaires. **N’activez pas la synchronisation vers ces partages !** |
| ![Étape 3 du processus](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Créez un partage vide pour chaque partage de fichiers que Data Box a créé automatiquement. Vérifiez que ce nouveau partage se trouve dans le même compte de stockage que le partage Data Box. [Guide pratique pour créer un partage de fichiers Azure](storage-how-to-create-file-share.md). |
| ![Étape 4 du processus](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Créez un groupe de synchronisation](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) dans un service de synchronisation de stockage et référencez le partage vide en tant que point de terminaison cloud. Répétez cette étape pour chaque partage de fichiers Data Box. Examinez le guide [Déployer Azure File Sync](storage-sync-files-deployment-guide.md) et suivez les étapes nécessaires pour configurer Azure File Sync. |
| ![Étape 5 du processus](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Ajoutez le répertoire de votre serveur en ligne comme point de terminaison de serveur](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Indiquez par la même occasion que vous avez déjà déplacé les fichiers vers Azure et référencez les partages intermédiaires. Il vous appartient d’activer ou de désactiver la hiérarchisation cloud, selon vos besoins. Au moment de créer un point de terminaison de serveur sur votre serveur en ligne, vous devez référencer le partage intermédiaire. Activez « Offline Data Transfer » (Transfert de données hors connexion) (image ci-dessous) dans le panneau du nouveau point de terminaison de serveur et référencez le partage intermédiaire qui doit résider dans le même compte de stockage que le point de terminaison cloud. La liste des partages disponibles est filtrée par compte de stockage et par partages pas encore synchronisés. |

![Visualisation de l’interface utilisateur du portail Azure où le transfert de données hors connexion est activé pendant la création d’un point de terminaison de serveur.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronisation du partage
Une fois que vous avez créé votre point de terminaison de serveur, la synchronisation commence. Pour chaque fichier présent sur le serveur, la synchronisation détermine si le fichier en question est aussi présent dans le partage intermédiaire où Data Box a déposé les fichiers. Si c’est le cas, au lieu de charger le fichier à partir du serveur, la synchronisation le copie à partir du partage intermédiaire. Si le fichier n’est pas présent dans le partage intermédiaire ou si une version plus récente est disponible sur le serveur local, la synchronisation charger le fichier à partir du serveur local.

> [!IMPORTANT]
> Vous pouvez uniquement activer le mode de migration en bloc pendant la création d’un point de terminaison de serveur. Une fois que le point de terminaison de serveur est établi, il n’existe actuellement aucun moyen d’intégrer les données migrées en bloc dans l’espace de noms à partir d’un serveur qui effectue déjà des synchronisations.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listes ACL et horodatages des fichiers et dossiers
Azure File Sync fait en sorte que les listes ACL des fichiers et dossiers soient synchronisées à partir du serveur en ligne, même si l’outil de migration en bloc qui a été utilisé n’a pas initialement transporté les listes ACL. Autrement dit, cela ne pose pas de problèmes si le partage intermédiaire ne contient pas de listes ACL sur les fichiers et les dossiers. Quand vous activez la fonctionnalité de migration de données hors connexion au moment de créer un point de terminaison de serveur, les listes ACL sont synchronisées à cet instant pour tous les fichiers présents sur le serveur. Il en va de même pour les horodatages créés et modifiés.

## <a name="shape-of-the-namespace"></a>Forme de l’espace de noms
La forme de l’espace de noms est déterminée par le contenu du serveur au moment où la synchronisation est activée. Si des fichiers sont supprimés du serveur local après la « capture instantanée » (et la migration) de la Data Box, ces fichiers ne sont pas être intégrés dans l’espace de noms en ligne, qui fait l’objet de la synchronisation. Ils figurent toujours sur le partage intermédiaire, mais ne sont jamais copiés. Il s’agit du comportement souhaité, car la synchronisation maintient l’espace de noms en fonction du serveur en ligne. La « capture instantanée » Data Box n’est qu’une zone intermédiaire destinée à copier avec efficacité les fichiers. Elle ne fait pas autorité pour déterminer la forme de l’espace de noms en ligne.

## <a name="finishing-bulk-migration-and-clean-up"></a>Finalisation de la migration en bloc et nettoyage
La capture d’écran ci-dessous présente le panneau des propriétés du point de terminaison de serveur sur le portail Azure. Dans la section Offline Data Transfer (Transfert de données hors connexion), vous pouvez observer l’état du processus. Il indique « In Progress » (En cours) ou « Completed » (Terminé).

À l’issue de la synchronisation initiale de l’espace de noms entier, le serveur cesse d’utiliser le partage de fichiers intermédiaire et les fichiers sont migrés en bloc par Data Box. Comme vous pouvez le constater, l’état du transfert de données hors connexion est passé à « Complete » (Terminé) dans les propriétés du point de terminaison de serveur. À ce stade, vous pouvez nettoyer le partage intermédiaire pour réduire les coûts :

1. Choisissez « Disable offline data transfer » (Désactiver le transfert de données hors connexion) dans les propriétés du point de terminaison de serveur quand l’état est « Completed » (Terminé).
2. Envisagez de supprimer le partage intermédiaire pour réduire les coûts. Sachant qu’il est peu probable que le partage intermédiaire contienne des listes ACL de fichiers et de dossiers, il est d’une utilité limitée. Pour des besoins de sauvegarde « ponctuels », créez plutôt une véritable [capture instantanée du partage de fichiers Azure de synchronisation](storage-snapshots-files.md). Vous pouvez [activer Sauvegarde Azure pour créer des captures instantanées]( ../../backup/backup-azure-files.md) selon une planification.

![Visualisation des propriétés du point de terminaison de serveur dans l’interface utilisateur du portail Azure où figurent l’état et les contrôles de désactivation du transfert de données hors connexion.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Vous ne devez désactiver ce mode que si l’état est « Completed » (Terminé) ou si vous souhaitez réellement abandonner l’opération en raison d’une mauvaise configuration. Si vous désactivez le mode au milieu d’un déploiement légitime, les fichiers commenceront à se charger à partir du serveur, même si votre partage intermédiaire est toujours disponible.

> [!IMPORTANT]
> Une fois le transfert de données hors connexion désactivé, il n’existe aucun moyen de la réactiver, même si le partage intermédiaire de la migration en bloc est toujours disponible.

## <a name="next-steps"></a>Étapes suivantes
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
