---
title: Activer la suppression réversible pour les objets blob
titleSuffix: Azure Storage
description: Activez la suppression réversible pour les objets blob afin de protéger les données blob contre les suppressions ou les remplacements accidentels.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554113"
---
# <a name="enable-soft-delete-for-blobs"></a>Activer la suppression réversible pour les objets blob

La suppression réversible d’objets blob protège un objet blob et ses versions, instantanés et métadonnées contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de rétention, vous pouvez restaurer l’objet blob à son état au moment de la suppression. Une fois la période de conservation expirée, l’objet est supprimé définitivement. Pour plus d’informations sur la suppression réversible d’objets blob, consultez [Suppression réversible pour les objets blob](soft-delete-blob-overview.md).

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Activer la suppression réversible de blob

La suppression réversible d’objets blob est désactivée par défaut pour un nouveau compte de stockage. Vous pouvez activer ou désactiver la suppression réversible pour un compte de stockage à tout moment à l’aide du portail Azure, de PowerShell ou d’Azure CLI.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer la suppression réversible d’objets blob pour votre compte de stockage à l’aide du portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.
1. Localisez l’option **Data Protection** dans **service Blob**.
1. Dans la section **Récupération**, sélectionnez **Activer la suppression réversible pour les objets blob**.
1. Spécifiez une période de conservation comprise entre 1 et 365 jours. Microsoft recommande une période de rétention minimale de sept jours.
1. Enregistrez vos modifications.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Capture d’écran représentant l’activation de la suppression réversible dans le portail Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour activer la suppression réversible d’objets blob avec PowerShell, appelez la commande [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) en spécifiant la période de rétention en jours.

L’exemple suivant active la suppression réversible d’objets blob et définit la période de rétention sur sept jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Pour vérifier les paramètres actuels de la suppression réversible d’objets blob, appelez la commande [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-CLI)

Pour activer la suppression réversible d’objets blob avec Azure CLI, appelez la commande [az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) en spécifiant la période de rétention en jours.

L’exemple suivant active la suppression réversible d’objets blob et définit la période de rétention sur sept jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Pour vérifier les paramètres actuels de la suppression réversible d’objets blob, appelez la commande [az storage account blob-service-properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md)
