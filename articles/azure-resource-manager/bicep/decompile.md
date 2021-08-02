---
title: Décompiler le JSON d’un modèle ARM dans un fichier Bicep
description: Décrit les commandes qui permettent de décompiler les modèles Azure Resource Manager dans des fichiers Bicep.
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9244eb0a076785c424603dc79ba13af6c29fb03
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537032"
---
# <a name="decompiling-arm-template-json-to-bicep"></a>Décompilation du JSON d’un modèle ARM dans un fichier Bicep

Cet article explique comment décompiler des modèles Azure Resource Manager (modèles ARM) dans des fichiers Bicep. Vous devez installer [l’interface CLI Bicep](./install.md) pour pouvoir exécuter les commandes de conversion.

La décompilation d’un modèle ARM vous aide à démarrer avec le développement Bicep. Si vous disposez d’une bibliothèque de modèles ARM et que vous souhaitez utiliser Bicep pour vos futures tâches de développement, vous pouvez les décompiler dans des fichiers Bicep. Toutefois, pour l’implémentation des bonnes pratiques Bicep, vous devrez peut-être réviser le fichier Bicep.

Cet article explique comment exécuter la commande `decompile` dans Azure CLI. Si vous n’utilisez pas Azure CLI, exécutez la commande sans `az` au début de celle-ci. Par exemple, `az bicep decompile` devient ``bicep decompile``.

## <a name="decompile-from-json-to-bicep"></a>Décompiler du JSON dans un fichier Bicep

Pour décompiler le JSON d’un modèle ARM dans un fichier Bicep, utilisez ceci :

```azurecli
az bicep decompile --file main.json
```

La commande crée un fichier nommé _main.bicep_ dans le répertoire où se trouve le modèle ARM.

> [!CAUTION]
> La décompilation tente de convertir le fichier, mais il n’existe aucun mappage garanti entre le JSON du modèle ARM et Bicep. Vous devrez peut-être corriger des avertissements et des erreurs dans le fichier Bicep généré. Il est également possible que la décompilation échoue si une conversion exacte n’est pas possible. Pour signaler des problèmes ou des conversions inexactes, [signalez un problème](https://github.com/Azure/bicep/issues).

Les commandes decompile et [build](bicep-cli.md#build) produisent des modèles qui sont équivalents d’un point de vue fonctionnel. Toutefois, ils ne sont pas nécessairement identiques dans l’implémentation. La conversion d’un modèle de JSON en Bicep, puis à nouveau en JSON, produit selon toute probabilité un modèle présentant une syntaxe différente de celle du modèle d’origine. Une fois déployés, les modèles convertis produisent les mêmes résultats.

## <a name="fix-conversion-issues"></a>Corriger les problèmes de conversion

Supposons que vous disposiez du modèle ARM suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
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

Quand vous le décompilez, vous obtenez :

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var storageAccountName_var = 'store${uniqueString(resourceGroup().id)}'

resource storageAccountName 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName_var
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName_var
```

Le fichier décompilé fonctionne, mais il contient des noms que vous souhaiterez peut-être changer. La variable `var storageAccountName_var` a une convention de nommage inhabituelle. Nous allons la remplacer par ceci :

```bicep
var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'
```

La ressource a un nom symbolique que vous souhaiterez peut-être changer. Au lieu de `storageAccountName` pour le nom symbolique, utilisez `exampleStorage`.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
```

Étant donné que vous avez remplacé le nom de la variable par le nom du compte de stockage, vous devez changer son emplacement d’utilisation.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
```

Dans la sortie, utilisez ceci :

```bicep
output storageAccountName string = uniqueStorageName
```

Voici le fichier dans son intégralité :

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'

resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = uniqueStorageName
```

## <a name="export-template-and-convert"></a>Exportation d’un modèle et conversion

Vous pouvez exporter le modèle pour un groupe de ressources, puis le passer directement à la commande `decompile`. L’exemple suivant montre comment décompiler un modèle exporté.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile --file main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

[Exportez le modèle](../templates/export-template-portal.md) via le portail.

Utilisez `bicep decompile <filename>` sur le fichier téléchargé.

---

## <a name="side-by-side-view"></a>Vue côte à côte

[Bicep Playground](https://aka.ms/bicepdemo) vous permet d’afficher le modèle ARM et le fichier Bicep équivalent côte à côte. Vous pouvez sélectionner un exemple de modèle pour afficher les deux versions. Vous pouvez également sélectionner `Decompile` pour charger votre propre modèle ARM et afficher le fichier Bicep équivalent.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur toutes les commandes CLI Bicep, consultez [commandes CLI Bicep](bicep-cli.md).
