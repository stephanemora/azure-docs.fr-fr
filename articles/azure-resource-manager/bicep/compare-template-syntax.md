---
title: Comparer la syntaxe des modèles Azure Resource Manager dans JSON et Bicep
description: Compare des modèles Azure Resource Manager développés avec JSON et Bicep, et montre comment opérer une conversion entre les langages.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a3df1189b264b092a7575cd976c23d89aa12bf18
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537117"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparaison de JSON et Bicep pour les modèles

Cet article compare les syntaxes Bicep et JSON pour les modèles Azure Resource Manager (modèles ARM). Dans la plupart des cas, Bicep fournit une syntaxe moins détaillée que l’équivalent dans JSON.

Si vous êtes familiarisé avec l’utilisation de JSON pour développer des modèles ARM, utilisez les exemples suivants pour découvrir la syntaxe équivalente pour Bicep.

## <a name="expressions"></a>Expressions

Pour créer une expression :

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Paramètres

Pour déclarer un paramètre avec une valeur par défaut :

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Pour obtenir une valeur de paramètre :

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variables

Pour déclarer une variable :

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Pour obtenir une valeur de variable :

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Chaînes

Pour concaténer des chaînes :

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Opérateurs logiques

Pour retourner l’opérateur logique **AND** :

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Pour définir une valeur de manière conditionnelle :

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Étendue du déploiement

Pour définir l’étendue cible du déploiement :

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Ressources

Pour déclarer une ressource :

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

Pour déployer une ressource de manière conditionnelle :

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

Pour définir une propriété de ressource :

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Pour obtenir l’ID d’une ressource dans un modèle :

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Boucles

Pour itérer au sein des éléments d’un tableau ou d’un total :

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Dépendances des ressources

Pour Bicep, vous pouvez définir une dépendance explicite, mais cette approche n’est pas recommandée. En revanche, reposez-vous sur les dépendances implicites. Une dépendance implicite est créée lorsqu’une déclaration de ressource référence l’identificateur d’une autre ressource.

L’exemple suivant montre une interface réseau avec une dépendance implicite sur un groupe de sécurité réseau. Il fait référence au groupe de sécurité réseau avec `nsg.id`.

```bicep
resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  ...
}

resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: nic1Name
  location: location
  properties: {
    ...
    networkSecurityGroup: {
      id: nsg.id
    }
  }
}
```

Si vous devez définir une dépendance explicite, utilisez :

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Ressources de référence

Pour obtenir une propriété à partir d’une ressource dans le modèle :

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Pour obtenir une propriété d’une ressource existante qui n’est pas déployée dans le modèle :

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Sorties

Pour sortir une propriété d’une ressource dans le modèle :

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Réutilisation du code

Pour scinder une solution en plusieurs fichiers :

* Pour Bicep, utilisez des [modules](modules.md).
* Pour les modèles ARM, utilisez des [modèles liés](../templates/linked-templates.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le langage Bicep, consultez le [Guide de démarrage rapide de Bicep](./quickstart-create-bicep-use-visual-studio-code.md).
* Pour en savoir plus sur la conversion de modèles entre les langages, consultez [Conversion de modèles ARM entre JSON et Bicep](decompile.md).
