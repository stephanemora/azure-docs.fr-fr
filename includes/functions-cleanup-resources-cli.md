---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 50d9e0e8ed9e6ac3d406570aa25d3a14cedab9b5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720572"
---
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez à l’[étape suivante](#next-steps), qui consiste à ajouter une liaison de sortie de file d’attente Stockage Azure, gardez toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---
