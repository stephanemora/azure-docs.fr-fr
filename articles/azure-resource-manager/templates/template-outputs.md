---
title: Sorties dans des modèles
description: Explique comment définir des valeurs de sortie dans un modèle Azure Resource Manager (ARM) et un fichier Bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 91feb1a0b653e4b6e96e38df57f87af27e4676f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703832"
---
# <a name="outputs-in-arm-templates"></a>Sorties dans les modèles ARM

Cet article explique comment définir des valeurs de sortie dans un modèle Azure Resource Manager (ARM) et un fichier Bicep. Vous utilisez des sorties quand vous devez retourner des valeurs à partir des ressources déployées.

Le format de chaque valeur de sortie doit résoudre l’un des [types de données](template-syntax.md#data-types).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Définir des valeurs de sortie

L’exemple suivant montre comment renvoyer une propriété à partir d’une ressource déployée.

# <a name="json"></a>[JSON](#tab/json)

Pour JSON, ajoutez la section `outputs` au modèle. La valeur de sortie obtient le nom de domaine complet pour une adresse IP publique.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pour Bicep, utilisez le mot clé `output`.

Dans l’exemple suivant, `publicIP` est l’identificateur d’une adresse IP publique déployée dans le fichier Bicep. La valeur de sortie obtient le nom de domaine complet pour l’adresse IP publique.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Si vous avez besoin de générer une propriété dont le nom comporte un trait d’union, utilisez des crochets autour du nom au lieu de la notation par points. Par exemple, utilisez `['property-name']` au lieu de `.property-name`.

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Sortie conditionnelle

Vous pouvez renvoyer une valeur de manière conditionnelle. En général, vous utilisez une sortie conditionnelle quand vous avez [déployé de manière conditionnelle](conditional-resource-deployment.md) une ressource. L’exemple suivant montre comment retourner de façon conditionnelle l’ID de ressource pour une adresse IP publique si une nouvelle a été déployée :

# <a name="json"></a>[JSON](#tab/json)

Dans JSON, ajoutez l’élément `condition` pour définir si la sortie est renvoyée.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pour spécifier une sortie conditionnelle dans Bicep, utilisez l'opérateur `?`. L’exemple suivant renvoie une URL de point de terminaison ou une chaîne vide en fonction d’une condition.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Pour obtenir un exemple simple de sortie conditionnelle, consultez [Modèle de sortie conditionnelle](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Nombre dynamique de sorties

Dans certains scénarios, vous ne connaissez pas le nombre d’instances d’une valeur que vous devez retourner lors de la création du modèle. Vous pouvez renvoyer un nombre variable de valeurs en utilisant une sortie itérative.

# <a name="json"></a>[JSON](#tab/json)

Dans JSON, ajoutez l'élément `copy` pour itérer une sortie.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

La sortie itérative n’est pas disponible actuellement pour Bicep.

---

Pour plus d’informations, voir [Itération de sorties dans des modèles ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modèles liés

Dans les modèles JSON, vous pouvez déployer des modèles associés à l’aide de [modèles liés](linked-templates.md). Pour récupérer la valeur de sortie à partir d’un modèle lié, utilisez la fonction [reference](template-functions-resource.md#reference) dans le modèle parent. La syntaxe dans le modèle parent est la suivante :

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

L’exemple suivant montre comment définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un modèle lié.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Si le nom de la propriété comporte un trait d’union, utilisez des crochets autour du nom au lieu de la notation par points.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section de sortie d’un [modèle imbriqué](linked-templates.md#nested-template). Pour renvoyer les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

Le [modèle d’adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) crée une adresse IP publique et génère en sortie l’ID de ressource. Le [modèle d’équilibreur de charge](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) est lié au modèle précédent. Utilise l’ID de ressource dans la sortie durant la création de l’équilibreur de charge.

## <a name="modules"></a>Modules

Dans les fichiers Bicep, vous pouvez déployer des modèles associés en utilisant des modules. Pour récupérer une valeur de sortie à partir d’un module, utilisez la syntaxe suivante :

```bicep
<module-name>.outputs.<property-name>
```

L’exemple suivant montre comment définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un module. Le nom du module est `publicIP`.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Exemple de modèle

Le modèle suivant ne déploie aucune ressource. Il montre quelques façons de renvoyer des sorties de différents types.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep ne prend pas en charge les boucles pour le moment.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Obtenir des valeurs de sortie

Une fois le déploiement terminé, les valeurs de sortie sont automatiquement retournées dans les résultats du déploiement.

Pour obtenir des valeurs de sortie à partir de l’historique des déploiements, vous pouvez utiliser un script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés disponibles pour les sorties, consultez [Présentation de la structure et de la syntaxe des modèles ARM](template-syntax.md).
