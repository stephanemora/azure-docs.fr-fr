---
title: Sorties dans Bicep
description: Décrit comment définir des variables dans Bicep
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 95366013ed972e0251c99732ea4b344b5b94e743
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026446"
---
# <a name="outputs-in-bicep"></a>Sorties dans Bicep

Cet article explique comment définir des valeurs de sortie dans un modèle Azure Resource Manager (ARM) et un fichier Bicep. Vous utilisez des sorties quand vous devez retourner des valeurs à partir des ressources déployées.

Le format de chaque valeur de sortie doit résoudre l’un des [types de données](data-types.md).

## <a name="define-output-values"></a>Définir des valeurs de sortie

L’exemple suivant montre comment utiliser le mot clé `output` pour renvoyer une propriété à partir d’une ressource déployée.

Dans l’exemple suivant, `publicIP` est l’identificateur (nom symbolique) d’une adresse IP publique déployée dans le fichier Bicep. La valeur de sortie obtient le nom de domaine complet pour l’adresse IP publique.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Si vous avez besoin de générer une propriété dont le nom comporte un trait d’union, utilisez des crochets autour du nom au lieu de la notation par points. Par exemple, utilisez `['property-name']` au lieu de `.property-name`.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>Sortie conditionnelle

Vous pouvez renvoyer une valeur de manière conditionnelle. En général, vous utilisez une sortie conditionnelle quand vous avez [déployé de manière conditionnelle](conditional-resource-deployment.md) une ressource. L’exemple suivant montre comment retourner de façon conditionnelle l’ID de ressource pour une adresse IP publique si une nouvelle a été déployée :

Pour spécifier une sortie conditionnelle dans Bicep, utilisez l'opérateur `?`. L’exemple suivant renvoie une URL de point de terminaison ou une chaîne vide en fonction d’une condition.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
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

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>Nombre dynamique de sorties

Dans certains scénarios, vous ne connaissez pas le nombre d’instances d’une valeur que vous devez retourner lors de la création du modèle. Vous pouvez renvoyer un nombre variable de valeurs en utilisant une sortie itérative.

Dans Bicep, ajoutez une expression `for` qui définit les conditions de la sortie dynamique. L’exemple suivant itère au sein d’un tableau.

```bicep
param nsgLocation string = resourceGroup().location
param nsgNames array = [
  'nsg1'
  'nsg2'
  'nsg3'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in nsgNames: {
  name: name
  location: nsgLocation
}]

output nsgs array = [for (name, i) in nsgNames: {
  name: nsg[i].name
  resourceId: nsg[i].id
}]
```

Vous pouvez également effectuer une itération sur une plage d’entiers. Pour plus d’informations, voir [Itération de sorties dans Bicep](loop-outputs.md).

## <a name="modules"></a>Modules

Vous pouvez déployer des modèles associés en utilisant des modules. Pour récupérer une valeur de sortie à partir d’un module, utilisez la syntaxe suivante :

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

Bicep ne prend pas en charge les boucles pour le moment.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

## <a name="get-output-values"></a>Obtenir des valeurs de sortie

Une fois le déploiement terminé, les valeurs de sortie sont automatiquement retournées dans les résultats du déploiement.

Pour obtenir des valeurs de sortie à partir de l’historique des déploiements, vous pouvez utiliser un script Azure CLI ou Azure PowerShell.

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

* Pour plus d’informations sur les propriétés disponibles pour les sorties, consultez [Présentation de la structure et de la syntaxe de Bicep](./file.md).
