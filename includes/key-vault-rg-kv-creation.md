---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512796"
---
Ce guide de démarrage rapide utilise un coffre de clés Azure créé au préalable. Vous pouvez créer un coffre de clés en suivant les étapes décrites dans le [guide de démarrage rapide d’Azure CLI](/azure/key-vault/general/quick-create-cli), le [guide de démarrage rapide d’Azure PowerShell](/azure/key-vault/general/quick-create-powershell) ou le [guide de démarrage rapide du portail Azure](/azure/key-vault/general/quick-create-portal). 

Sinon, vous pouvez simplement exécuter les commandes Azure CLI ou Azure PowerShell ci-dessous.

> [!Important]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```