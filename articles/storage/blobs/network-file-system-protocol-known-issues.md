---
title: Problèmes connus avec NFS 3.0 dans le Stockage Blob Azure | Microsoft Docs
description: Découvrez les limitations et les problèmes connus de la prise en charge du protocole NFS 3.0 dans le Stockage Blob Azure.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8af0b4a5c26d70f9fdedc30d07c8953bfee76fb4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727127"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Problèmes connus de la prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure

Cet article décrit les limitations et les problèmes connus de la prise en charge du protocole NFS 3.0 dans le Stockage Blob Azure.

## <a name="nfs-30-support"></a>Prise en charge NFS 3.0

- La prise en charge NFS 3.0 ne peut pas être activée sur les comptes de stockage existants.

- Une fois activée, la prise en charge NFS 3.0 ne peut pas être désactivée dans un compte de stockage.

## <a name="nfs-30-features"></a>Fonctionnalités NFS 3.0

Les fonctionnalités NFS 3.0 suivantes ne sont pas encore prises en charge.

- NFS 3.0 sur UDP. Seul NFS 3.0 sur TCP est pris en charge.

- Verrouillage des fichiers avec NLM (Network Lock Manager). Les commandes de montage doivent inclure le paramètre `-o nolock`.

- Montage des sous-répertoires. Vous pouvez monter uniquement le répertoire racine (conteneur).

- Répertorier les montages (par exemple : à l’aide de la commande `showmount -a`)

- Répertorier les exportations (par exemple : à l’aide de la commande `showmount -e`)

- Lien physique

- Exportation d’un conteneur en lecture seule

## <a name="nfs-30-clients"></a>Clients NFS 3.0

Le client Windows pour NFS n’est pas encore pris en charge

## <a name="blob-storage-features"></a>Fonctionnalités du Stockage Blob

Les fonctionnalités du Stockage Blob suivantes ne sont pas encore prises en charge.

- [Sécurité d’Azure Active Directory (AD)](../common/authorize-data-access.md?toc=/azure/storage/blobs/toc.json)
- [Intégration à la Sauvegarde Azure](../../backup/blob-backup-overview.md)
- [Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json)
- [Étiquettes d’index d’objet blob](storage-blob-index-how-to.md)
- [Événements de stockage Blob](storage-blob-event-overview.md)
- [Contrôle de version des blobs](versioning-enable.md)
- [Modifier le flux](storage-blob-change-feed.md)
- [Basculement de compte géré par le client](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json)
- [Clés fournies par le client pour le chiffrement du Stockage Azure](encryption-customer-provided-keys.md) 
- [Étendues de chiffrement](encryption-scope-overview.md)
- [Suivi de l’heure du dernier accès pour la gestion de cycle de vie](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview)
- [Réplication d'objets blob de blocs](object-replication-overview.md)
- [Objets blob de pages](storage-blobs-introduction.md#blobs)
- [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md)
- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Suppression réversible des conteneurs](soft-delete-container-overview.md)
- [Instantanés d’objet blob](snapshots-overview.md)
- [Hébergement de sites web statiques](storage-blob-static-website.md)

## <a name="blob-storage-apis"></a>API Stockage Blob

NFS 3.0, les API Blob et les API Data Lake Storage Gen2 peuvent utiliser les mêmes données. 

Cette section décrit les problèmes et les limitations liés à l’utilisation simultanée de mêmes données par NFS 3.0, par les API Blob et par les API Data Lake Storage Gen2. 

- Il n’est pas possible d’utiliser NFS 3.0 et l’API Blob et les API Data Lake Storage pour écrire des données dans une même instance de fichier. Si vous écrivez des données dans un fichier à l’aide de NFS, les blocs de ce fichier ne seront pas visibles pour les appels émis vers l’API Blob  [Get Block List](/rest/api/storageservices/get-block-list). La seule exception concerne les cas de remplacement. Il est en effet possible de remplacer un fichier ou un objet blob à l’aide de n’importe quelle API. Vous pouvez également remplacer les données avec NFS 3.0 à l’aide de l’option zero-truncate.

- Lorsque vous utilisez l’opération [Lister les objets BLOB](/rest/api/storageservices/list-blobs) sans spécifier de délimiteur, les résultats incluront à la fois des répertoires et des objets BLOB. Si vous choisissez d’utiliser un délimiteur, n’utilisez qu’une barre oblique (`/`). Il s’agit du seul délimiteur pris en charge.

- Si vous utilisez l’API [Supprimer un objet BLOB](/rest/api/storageservices/delete-blob) pour supprimer un répertoire, ce répertoire est supprimé uniquement s’il est vide. Cela signifie que vous ne pouvez pas utiliser les répertoires de suppression de l’API d’objet BLOB de manière récursive.

Ces API REST BLOB ne sont pas prises en charge :

* [Placer BLOB (Page)](/rest/api/storageservices/put-blob)
* [Put Page](/rest/api/storageservices/put-page)
* [Obtenir les portées de page](/rest/api/storageservices/get-page-ranges)
* [Copie incrémentielle BLOB](/rest/api/storageservices/incremental-copy-blob)
* [Placer la page à partir de l’URL](/rest/api/storageservices/put-page-from-url)
* [Put Block List](/rest/api/storageservices/put-block-list)

Les disques de machine virtuelle non gérés ne sont pas pris en charge dans les comptes qui ont un espace de noms hiérarchique. Si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machine virtuelle non gérés dans un compte de stockage pour lequel la fonctionnalité espace de noms hiérarchique n’est pas activée. 

## <a name="see-also"></a>Voir aussi

- [Prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure](network-file-system-protocol-support.md)
- [Monter le stockage Blob à l’aide du protocole NFS (Network File System) 3.0](network-file-system-protocol-support-how-to.md)