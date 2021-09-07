---
title: Obtenir les informations de configuration d’un compte de stockage
titleSuffix: Azure Storage
description: Utilisez le portail Azure, PowerShell ou Azure CLI pour récupérer les propriétés de configuration du compte de stockage, y compris l’ID de ressource Azure Resource Manager, l’emplacement du compte, le type de compte et la référence SKU de réplication.
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573649"
---
# <a name="get-storage-account-configuration-information"></a>Obtenir les informations de configuration d’un compte de stockage

Cet article explique comment obtenir les informations de configuration et les propriétés d’un compte de stockage Azure à l’aide du portail Azure, de PowerShell ou d’Azure CLI.

## <a name="get-the-resource-id-for-a-storage-account"></a>Obtenir l’ID de ressource d’un compte de stockage

Chaque ressource Azure Resource Manager est associée à un ID de ressource qui permet de l’identifier. Certaines opérations nécessitent que vous fournissiez l’ID de ressource. Vous pouvez obtenir l’ID de ressource d’un compte de stockage à l’aide du portail Azure, de PowerShell ou d’Azure CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour afficher l’ID de ressource Azure Resource Manager d’un compte de stockage dans le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la page **Vue d’ensemble**, dans la section **Essentials**, sélectionnez le lien **Affichage JSON**.
1. L’ID de ressource du compte de stockage est affiché en haut de la page.

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="Capture d’écran montrant comment copier l’ID de ressource du compte de stockage à partir du portail":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour retourner l’ID de ressource Azure Resource Manager d’un compte de stockage avec PowerShell, vous devez avoir installé le module [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). Ensuite, appelez la commande [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) pour retourner le compte de stockage et voir son ID de ressource :

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour retourner l’ID de ressource Azure Resource Manager d’un compte de stockage avec Azure CLI, appelez la commande [az storage account show](/cli/azure/storage/account#az_storage_account_show) et interrogez l’ID de ressource :

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

Vous pouvez également obtenir l’ID de ressource d’un compte de stockage en appelant l’opération [StorageAccountGetProperties](/rest/api/storagerp/storage-accounts/get-properties) dans l’API REST.

Pour plus d’informations sur les types de ressources gérés par Azure Resource Manager, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>Obtenir le type de compte, l’emplacement ou la référence SKU de réplication d’un compte de stockage

Le type de compte, l’emplacement et la référence SKU de réplication font partie des propriétés d’un compte de stockage. Vous pouvez utiliser le portail Azure, PowerShell ou Azure CLI pour voir ces valeurs.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour afficher le type de compte, l’emplacement ou la référence SKU de réplication d’un compte de stockage dans le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Ces propriétés sont visibles dans la page **Vue d’ensemble** de la section **Essentials**.

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="Capture d’écran montrant la configuration du compte dans le portail":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour afficher le type de compte, l’emplacement ou la référence SKU de réplication d’un compte de stockage avec PowerShell, appelez la commande [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) pour retourner le compte de stockage, puis vérifiez les propriétés :

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher le type de compte, l’emplacement ou la référence SKU de réplication d’un compte de stockage avec PowerShell, appelez la commande [az storage account show](/cli/azure/storage/account#az_storage_account_show) puis interrogez les propriétés :

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du compte de stockage](storage-account-overview.md)
- [Créez un compte de stockage](storage-account-create.md)
