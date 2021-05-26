---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 5a94cdae7cc332b8e0df5b2ebdc6ae57b67c8a68
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385991"
---
Un fournisseur de ressources est un service qui fournit des ressources Azure. Utilisez la commande Azure CLI [az provider register](/cli/azure/provider#az_provider_register) ou la cmdlet Azure PowerShell [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) pour inscrire le fournisseur de ressources du Registre confidentiel, « microsoft.ConfidentialLedger ».

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

Vous pouvez vérifier que l’inscription est terminée à l’aide de la commande Azure CLI [az provider register](/cli/azure/provider#az_provider_show) ou de la cmdlet Azure PowerShell [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
