---
title: Prise en charge des fonctionnalités de Stockage Blob dans les comptes de stockage Azure
description: Déterminez le niveau de prise en charge pour chaque fonctionnalité de compte de stockage selon le type de compte de stockage, ainsi que les paramètres activés sur le compte.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.openlocfilehash: e22e7dcfde1782600cdb53814014e6080ffbbfa1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271628"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Prise en charge des fonctionnalités de Stockage Blob dans les comptes de Stockage Azure

Cet article indique si une fonctionnalité est entièrement prise en charge (en disponibilité générale), prise en charge au niveau de la préversion ou n’est pas encore prise en charge. Les niveaux de support sont affectés par le type de compte de stockage et l’activation ou non de certaines fonctionnalités ou protocoles sur le compte.

Les éléments figurant dans ces tableaux seront modifiés au fil du temps, car la prise en charge continue de s’étendre.

## <a name="standard-general-purpose-v2-accounts"></a>Compte v2 standard à usage général

| Fonctionnalité Stockage | Stockage Blob (prise en charge par défaut) | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  |
|---------------|-------------------|---|---|
| [Niveau d’accès - archive](access-tiers-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Niveau d’accès - froid](access-tiers-overview.md)    | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)|
| [Niveau d’accès - chaud](access-tiers-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Accès public anonyme](anonymous-read-access-configure.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)|
| [Sécurité Azure Active Directory](authorize-access-azure-active-directory.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Inventaire des objets blob](blob-inventory.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Étiquettes d’index d’objet blob](storage-manage-find-blobs.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Instantanés d’objet blob](snapshots-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Non](../media/icons/no-icon.png) |
| [API Stockage Blob](reference.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>3</sup> | ![Oui](../media/icons/yes-icon.png) <sup>3</sup>|
| [Commandes Azure CLI de stockage Blob](storage-quickstart-blobs-cli.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Événements de stockage Blob](storage-blob-event-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>3</sup>  | ![Non](../media/icons/no-icon.png) |
| [Commandes PowerShell de stockage Blob](storage-quickstart-blobs-powershell.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Contrôle de version des blobs](versioning-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Flux de modification](storage-blob-change-feed.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Domaines personnalisés](storage-custom-domain-name.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Basculement de compte géré par le client](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Clés gérées par le client (chiffrement)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Clés fournies par le client (chiffrement)](encryption-customer-provided-keys.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Options de redondance de données](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Étendues de chiffrement](encryption-scope-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Stockage non modifiable](immutable-storage-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Suivi de l’heure du dernier accès pour la gestion de cycle de vie](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Stratégies de gestion du cycle de vie (supprimer l’objet blob)](./lifecycle-management-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Stratégies de gestion du cycle de vie (hiérarchisation)](./lifecycle-management-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Connexion à Azure Monitor](./monitor-blob-storage.md) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Mesures dans Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Réplication d'objets blob de blocs](object-replication-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Objets blob de pages](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Oui](../media/icons/yes-icon.png) |![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Suppression réversible pour les objets blob](./soft-delete-blob-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![Non](../media/icons/no-icon.png) |
| [Suppression réversible des conteneurs](soft-delete-container-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Sites web statiques](storage-blob-static-website.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Journaux Storage Analytics (classique)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>3</sup> | ![Non](../media/icons/no-icon.png) |
| [Métriques Storage Analytics (classiques)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

<sup>2</sup> La fonctionnalité est prise en charge dans la préversion.

<sup>3</sup>    Consultez [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Ces problèmes s'appliquent à tous les comptes pour lesquels la fonction d'espace de noms hiérarchique est activée.

## <a name="premium-block-blob-accounts"></a>Comptes d'objets blob de blocs Premium

| Fonctionnalité Stockage | Stockage Blob (prise en charge par défaut) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|---------------|-------------------|---|---|
| [Niveau d’accès - archive](access-tiers-overview.md)  | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Niveau d’accès - froid](access-tiers-overview.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Niveau d’accès - chaud](access-tiers-overview.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Accès public anonyme](anonymous-read-access-configure.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Sécurité Azure Active Directory](authorize-access-azure-active-directory.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Inventaire des objets blob](blob-inventory.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Étiquettes d’index d’objet blob](storage-manage-find-blobs.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Instantanés d’objet blob](snapshots-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Non](../media/icons/no-icon.png) |
| [API Stockage Blob](reference.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>3</sup> | ![Oui](../media/icons/yes-icon.png) <sup>3</sup>|
| [Commandes Azure CLI de stockage Blob](storage-quickstart-blobs-cli.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Événements de stockage Blob](storage-blob-event-overview.md) | ![Oui](../media/icons/yes-icon.png)  <sup>3</sup>  | ![Oui](../media/icons/yes-icon.png) <sup>3</sup>| ![Non](../media/icons/no-icon.png) |
| [Commandes PowerShell de stockage Blob](storage-quickstart-blobs-powershell.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Contrôle de version des blobs](versioning-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Flux de modification](storage-blob-change-feed.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Domaines personnalisés](storage-custom-domain-name.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Basculement de compte géré par le client](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Clés gérées par le client (chiffrement)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Clés fournies par le client (chiffrement)](encryption-customer-provided-keys.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Options de redondance de données](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Étendues de chiffrement](encryption-scope-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Stockage non modifiable](immutable-storage-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Suivi de l’heure du dernier accès pour la gestion de cycle de vie](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Stratégies de gestion du cycle de vie (supprimer l’objet blob)](./lifecycle-management-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Stratégies de gestion du cycle de vie (hiérarchisation)](./lifecycle-management-overview.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Connexion à Azure Monitor](./monitor-blob-storage.md) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Mesures dans Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Réplication d'objets blob de blocs](object-replication-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Objets blob de pages](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Suppression réversible pour les objets blob](./soft-delete-blob-overview.md)   | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![Non](../media/icons/no-icon.png) |
| [Suppression réversible des conteneurs](soft-delete-container-overview.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| [Sites web statiques](storage-blob-static-website.md) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |
| [Journaux Storage Analytics (classique)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui ](../media/icons/yes-icon.png) <sup>2</sup> <sup>3</sup> | ![Non](../media/icons/no-icon.png)|
| [Métriques Storage Analytics (classiques)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

<sup>2</sup> La fonctionnalité est prise en charge dans la préversion.

<sup>3</sup>    Consultez [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Ces problèmes s'appliquent à tous les comptes pour lesquels la fonction d'espace de noms hiérarchique est activée.

## <a name="see-also"></a>Voir aussi

- [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)

- [Problèmes connus concernant la prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure](network-file-system-protocol-known-issues.md)
