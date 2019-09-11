---
title: Emplacement des ressources dans un modèle Azure Resource Manager
description: Décrit comment définir un emplacement de ressource dans un modèle Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: tomfitz
ms.openlocfilehash: 6af5d82766533ef7bbacab04381db9a37502923e
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293763"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Définir l’emplacement des ressources dans un modèle Resource Manager

Lorsque vous déployez un modèle, vous devez fournir un emplacement pour chaque ressource. L’emplacement n’a pas à être le même que celui du groupe de ressources.

## <a name="get-available-locations"></a>Obtenir les emplacements disponibles

Différents types de ressources sont pris en charge à différents emplacements. Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez Azure PowerShell ou Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Utiliser le paramètre d’emplacement

Pour plus de souplesse lors du déploiement de votre modèle, utilisez un paramètre pour spécifier l’emplacement des ressources. Définissez la valeur par défaut du paramètre sur `resourceGroup().location`.

L’exemple suivant illustre le déploiement d’un compte de stockage dans un emplacement spécifié en tant que paramètre :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour plus d’informations sur les fichiers de modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
