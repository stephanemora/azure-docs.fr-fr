---
title: Réalimenter un objet blob archivé dans un niveau en ligne
titleSuffix: Azure Storage
description: Avant de pouvoir lire un blob qui se trouve dans le niveau archive, vous devez le réhydrater au niveau chaud ou froid. Vous pouvez réhydrater un blob soit en le copiant du niveau archive vers un niveau en ligne, soit en remplaçant son niveau archive par chaud ou froid.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: a1681640b97d6ee183eefd7f65edd9b2d944f0f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633865"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Réalimenter un objet blob archivé dans un niveau en ligne

Pour lire un objet blob qui se trouve dans le niveau Archive, vous devez d’abord réalimenter l’objet blob au niveau chaud ou froid. Vous pouvez réhydrater un blob d’une de ces deux manières :

- En le copiant dans un nouveau blob dans le niveau chaud ou froid avec l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) ou [Copier un objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url). Microsoft recommande cette option pour la plupart des scénarios.
- En changeant son niveau d’archive à chaud ou froid avec l’opération [Définir un niveau d’objet blob](/rest/api/storageservices/set-blob-tier).

Cette opération de réhydratation peut prendre jusqu’à 15 heures. Vous pouvez configurer Azure Event Grid pour déclencher un événement lorsque la réhydratation est terminée et exécuter le code d’application en réponse. Pour savoir comment gérer un événement qui exécute une fonction Azure quand l’opération de réhydratation de blob est terminée, consultez [Exécuter une fonction Azure en réponse à un événement de réhydratation de blob](archive-rehydrate-handle-event.md).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Réactiver un objet blob à l’aide d’une opération de copie

Pour réhydrater un blob à partir du niveau archive en le copiant sur un niveau en ligne, utilisez PowerShell, Azure CLI ou l’une des bibliothèques clientes de stockage Azure. Gardez à l’esprit que lorsque vous copiez un blob archivé sur un niveau en ligne, les blob source et de destination doivent avoir des noms différents.

Une fois l’opération de copie terminée, le blob de destination s’affiche dans le niveau archive. Le blob de destination est ensuite réhydraté au niveau en ligne que vous avez spécifié dans l’opération de copie. Lorsque le blob de destination est entièrement réhydraté, il devient disponible dans le nouveau niveau en ligne.

Les exemples suivants montrent comment copier un blob archivé avec PowerShell ou Azure CLI.

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour copier un blob archivé sur un niveau en ligne avec PowerShell, appelez la commande [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) et spécifiez le niveau cible et la priorité de réhydratation. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in hot tier with standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour copier un blob archivé sur un niveau en ligne avec Azure CLI, appelez la commande [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) et spécifiez le niveau cible et la priorité de réhydratation. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>Réactiver un objet blob en modifiant son niveau

Pour réalimenter un blob en modifiant son niveau de l’opération d’archivage à chaud ou froid, utilisez le portail Azure, PowerShell ou Azure CLI.

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour modifier le niveau d’un blob de l’archive à chaud ou froid dans le portail Azure, procédez comme suit :

1. Localisez le blob à réhydrater dans le portail Azure.
1. Sélectionnez le bouton **Plus** à droite de la page.
1. Sélectionnez **Modifier le niveau**.
1. Sélectionnez le niveau d'accès cible à partir de la liste déroulante **Niveau d'accès**.
1. À partir de la liste déroulante **Priorité de réactivation**, sélectionnez la priorité de réhydratation souhaitée. Gardez à l’esprit que la définition de la priorité de réhydratation sur *Élevée* entraîne généralement une réhydratation plus rapide, mais également un coût plus élevé.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Capture d’écran montrant comment réhydrater un blob à partir du niveau archive dans le portail Azure ":::

1. Sélectionnez le bouton **Enregistrer**.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour changer le niveau d’un blob d’archive à chaud ou froid avec PowerShell, utilisez la propriété **ICloudBlob** du blob pour renvoyer une référence .NET au blob, puis appelez la méthode **SetStandardBlobTier** sur cette référence. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour changer le niveau d’un blob d’archive à chaud ou froid avec Azure CLI, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>Réhydratez un grand nombre d’objets BLOB

Pour réhydrater un grand nombre d’objets BLOB à la fois, appelez l’opération [Blob Batch](/rest/api/storageservices/blob-batch) pour appeler [Set Blob Tier](/rest/api/storageservices/set-blob-tier) en tant qu’opération en bloc. Pour obtenir un exemple de code qui montre comment effectuer l’opération de traitement par lots, consultez [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Vérifier l’état d’une opération de réhydratation

Lorsque le blob est en cours de réhydratation, vous pouvez vérifier son état et sa priorité de réhydratation à l’aide du portail Azure, de PowerShell ou d’Azure CLI. La propriété de l’état peut renvoyer *rehydrate-pending-to-hot* ou *rehydrate-pending-to-cool*, selon le niveau cible pour l’opération de réhydratation. La propriété de la priorité de réhydratation renvoie *Standard* ou *Élevé*.

N’oubliez pas que la réhydratation d’un blob archivé peut prendre jusqu’à 15 heures, et interroger de manière répétée l’état du blob pour déterminer si la réhydratation est terminée est inefficace. L’utilisation d’Azure Event Grid pour capturer l’événement qui se déclenche lorsque la réhydratation est terminée offre de meilleures performances et une optimisation des coûts. Pour savoir comment exécuter une fonction Azure quand un événement se déclenche sur la réhydratation de blob, consultez [Exécuter une fonction Azure en réponse à un événement de réhydratation de blob](archive-rehydrate-handle-event.md).

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente dans le portail Azure, affichez la boîte de dialogue **Modifier le niveau** pour le blob :

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Capture d’écran montrant l’état de réhydratation d’un blob dans le portail Azure":::

Lorsque la réhydratation est terminée, vous pouvez voir dans le portail Azure que le blob entièrement réhydraté apparaît maintenant dans le niveau en ligne ciblé.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Capture d’écran montrant le blob réhydraté dans le niveau froid et le blob de journal écrit par le gestionnaire d’événements":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente avec PowerShell, appelez la commande [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) et vérifiez les propriétés **ArchiveStatus** et **RehydratePriority** du blob. Si la réhydratation est une opération de copie, vérifiez ces propriétés sur le blob de destination. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```powershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente avec Azure CLI, appelez la commande [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) et vérifiez les propriétés **rehydrationStatus** et **rehydratePriority** du blob de destination. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>Voir aussi

- [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md).
- [Vue d’ensemble de la réactivation des objets blob à partir du niveau Archive](archive-rehydrate-overview.md)
- [Exécuter une fonction Azure en réponse à un événement de réactivation d’objet blob](archive-rehydrate-handle-event.md)
- [Réaction aux événements de stockage Blob](storage-blob-event-overview.md)
