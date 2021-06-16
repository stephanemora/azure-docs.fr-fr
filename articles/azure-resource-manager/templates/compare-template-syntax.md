---
title: Comparer la syntaxe des modèles Azure Resource Manager dans JSON et Bicep
description: Compare des modèles Azure Resource Manager développés avec JSON et Bicep, et montre comment opérer une conversion entre les langages.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353283"
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

Pour définir la dépendance entre les ressources :

Pour Bicep, appuyez-vous sur la détection automatique des dépendances ou définissez manuellement une dépendance.

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

* Pour Bicep, utilisez des [modules](bicep-tutorial-add-modules.md).
* Pour JSON, utilisez des [modèles liés](linked-templates.md).

## <a name="recommendations"></a>Recommandations

* Dans la mesure du possible, évitez d’utiliser les fonctions [reference](template-functions-resource.md#reference) et [resourceId](template-functions-resource.md#resourceid) dans votre fichier Bicep. Lorsque vous référencez une ressource dans le même déploiement Bicep, utilisez l’identificateur de ressource à la place. Par exemple, si vous avez déployé une ressource dans votre fichier Bicep avec `stg` en tant qu’identificateur de ressource, utilisez une syntaxe comme `stg.id` ou `stg.properties.primaryEndpoints.blob` pour obtenir des valeurs de propriété. En utilisant l’identificateur de ressource, vous créez une dépendance implicite entre des ressources. Vous n’avez pas besoin de définir explicitement la dépendance avec la propriété dependsOn.
* Si la ressource n’est pas déployée dans le fichier Bicep, vous pouvez toujours obtenir une référence symbolique à la ressource à l’aide du mot clé **existing**.
* Utilisez une casse cohérente pour les identificateurs. Si vous n’êtes pas sûr du type de casse à utiliser, essayez la casse mixte. Par exemple : `param myCamelCasedParameter string`.
* Ajoutez une description à un paramètre uniquement quand la description fournit des informations essentielles aux utilisateurs. Vous pouvez utiliser des commentaires `//` pour certaines informations.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le langage Bicep, consultez le [Tutoriel Bicep](./bicep-tutorial-create-first-bicep.md).
* Pour en savoir plus sur la conversion de modèles entre les langages, consultez [Conversion de modèles ARM entre JSON et Bicep](bicep-decompile.md).
