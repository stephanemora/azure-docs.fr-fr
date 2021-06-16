---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe21da6b8cd2a3f7291ae4a27ef7de8040a2acd1
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110719996"
---
Ce guide de démarrage rapide utilise un coffre de clés Azure créé au préalable. Vous pouvez créer un coffre de clés en suivant les étapes décrites dans le [guide de démarrage rapide d’Azure CLI](../articles/key-vault/general/quick-create-cli.md), le [guide de démarrage rapide d’Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md) ou le [guide de démarrage rapide du portail Azure](../articles/key-vault/general/quick-create-portal.md). 

Sinon, vous pouvez simplement exécuter les commandes Azure CLI ou Azure PowerShell ci-dessous.

> [!Important]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---
