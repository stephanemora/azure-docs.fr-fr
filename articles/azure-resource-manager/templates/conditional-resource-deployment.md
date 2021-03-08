---
title: Déploiement conditionnel avec des modèles
description: Explique comment déployer une ressource de manière conditionnelle dans un modèle Azure Resource Manager (modèle ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741172"
---
# <a name="conditional-deployment-in-arm-templates"></a>Déploiement conditionnel dans des modèles ARM

Parfois, vous devez déployer une ressource facultative dans un modèle Azure Resource Manager (modèle ARM) ou un fichier Bicep. Pour les modèles JSON, utilisez l’élément `condition` pour spécifier si la ressource est déployée. Pour Bicep, utilisez le mot clé `if` pour spécifier si la ressource est déployée. La valeur de la condition est résolue en true ou false. Lorsque la valeur est true, la ressource est créée. Lorsque la valeur est false, la ressource n’est pas créée. La valeur ne peut être appliquée qu’à l’ensemble de la ressource.

> [!NOTE]
> L’exécution du déploiement conditionnel n’inclut pas les [ressources enfants](child-resource-name-type.md). Si vous souhaitez déployer une ressource et ses ressources enfants de manière conditionnelle, vous devez appliquer la même condition à chaque type de ressource.

## <a name="deploy-condition"></a>Condition de déploiement

Vous pouvez transmettre dans un paramètre une valeur indiquant si une ressource est déployée. L’exemple suivant opère un déploiement de manière conditionnelle dans une zone DNS.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Pour un exemple plus complexe, consultez [Serveur logique SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Ressource nouvelle ou existante

Vous pouvez utiliser un déploiement conditionnel pour créer une ressource ou en utiliser une existante. L’exemple suivant montre comment déployer un nouveau compte de stockage ou utiliser un compte de stockage existant.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Lorsque le paramètre `newOrExisting` a la valeur **new**, la condition donne le résultat true. Le compte de stockage est déployé. En revanche, quand le paramètre `newOrExisting` a la valeur **existing**, la condition donne le résultat false et le compte de stockage n’est pas déployé.

Pour un exemple de modèle complet qui utilise l’élément `condition`, consultez [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Machine virtuelle avec un réseau virtuel, un stockage et une adresse IP publique nouveaux ou existants).

## <a name="runtime-functions"></a>Fonctions de runtime

Si vous utilisez une fonction de [référence](template-functions-resource.md#reference) ou de [liste](template-functions-resource.md#list) avec une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée. Vous obtenez une erreur si la fonction fait référence à une ressource qui n’existe pas.

Utilisez la fonction [if](template-functions-logical.md#if) pour vous assurer que la fonction est évaluée uniquement pour les conditions lorsque la ressource est déployée. Consultez la [fonction if](template-functions-logical.md#if) pour obtenir un exemple de modèle qui utilise `if` et `reference` avec une ressource déployée de manière conditionnelle.

Vous définissez une [ressource comme étant dépendante](define-resource-dependency.md) d’une ressource conditionnelle exactement comme vous le feriez pour une autre ressource. Quand une ressource conditionnelle n’est pas déployée, Azure Resource Manager la supprime automatiquement des dépendances nécessaires.

## <a name="complete-mode"></a>Mode Complet

Si vous déployez un modèle en [mode complet](deployment-modes.md) et qu’une ressource n’est pas déployée parce que `condition` prend la valeur false, le résultat dépend de la version de l’API REST utilisée pour déployer le modèle. Si vous utilisez une version antérieure à 2019-05-10, la ressource **n’est pas supprimée**. Avec 2019-05-10 ou ultérieur, elle **est supprimée**. Les dernières versions d’Azure PowerShell et d’Azure CLI suppriment la ressource lorsque la condition a la valeur false.

## <a name="next-steps"></a>Étapes suivantes

* Pour lire un module Microsoft Learn qui aborde les conditions de déploiement, consultez [Gérer des déploiements cloud complexes à l’aide des fonctionnalités avancées de modèle ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Pour obtenir des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives aux modèles ARM](template-best-practices.md).
* Pour créer plusieurs instances d’une ressource, consultez [Itération de ressources dans des modèles ARM](copy-resources.md).
