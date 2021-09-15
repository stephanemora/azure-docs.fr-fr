---
title: Opérateurs d’accesseur Bicep
description: Décrit l’opérateur d’accès aux ressources Bicep et l’opérateur d’accès aux propriétés.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: b5eebb9b5dd6d39ae790b8fda7133e94ecd0cdb5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224281"
---
# <a name="bicep-accessor-operators"></a>Opérateurs d’accesseur Bicep

Les opérateurs d’accesseur sont utilisés pour accéder aux ressources et propriétés enfants sur les objets, et aux éléments dans un tableau. Vous pouvez également utiliser l’accesseur de propriété pour utiliser certaines fonctions.

| Opérateur | Nom |
| ---- | ---- |
| `[]` | [Accesseur d’index](#index-accessor) |
| `.`  | [Accesseur de fonction](#function-accessor) |
| `::` | [Accesseur de ressource imbriquée](#nested-resource-accessor) |
| `.`  | [Accesseur de propriété](#property-accessor) |

## <a name="index-accessor"></a>Accesseur d’index

`array[index]`

`object['index']`

Pour récupérer un élément dans un tableau, utilisez `[index]` et fournissez un entier pour l’index.

L’exemple suivant récupère un élément dans un tableau.

```bicep
var arrayVar = [
  'Coho'
  'Contoso'
  'Fabrikan'
]

output accessorResult string = arrayVar[1]
``` 

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| accessorResult | string | 'Contoso' |

Vous pouvez également utiliser l’accesseur d’index pour récupérer une propriété d’objet par nom. Vous devez utiliser une chaîne pour l’index, et non un entier. L’exemple suivant récupère une propriété sur un objet.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| accessorResult | string | 'Development' |

## <a name="function-accessor"></a>Accesseur de fonction

`resourceName.functionName()`

Deux fonctions ([getSecret](bicep-functions-resource.md#getsecret) et [list*](bicep-functions-resource.md#list)) prennent en charge l’opérateur d’accesseur pour l’appel de la fonction. Ces deux fonctions sont les seules qui prennent en charge l’opérateur d’accesseur.

### <a name="example"></a>Exemple

L’exemple suivant référence un coffre de clés existant, puis utilise `getSecret` pour transmettre un secret à un module.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="nested-resource-accessor"></a>Accesseur de ressource imbriquée

`parentResource::nestedResource`

Une ressource imbriquée est une ressource déclarée dans une autre ressource. Utilisez l’accesseur de ressource imbriqué `::` pour accéder à ces ressources imbriquées à partir de l’extérieur de la ressource parente.

Dans la ressource parente, vous référencez la ressource imbriquée uniquement avec le nom symbolique. Vous devez utiliser l’accesseur de ressource imbriquée uniquement quand vous référencez la ressource imbriquée à partir de l’extérieur de la ressource parente.

### <a name="example"></a>Exemple

L’exemple suivant montre comment référencer une ressource imbriquée au sein de la ressource parente et à partir de l’extérieur de la ressource parente.

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>Accesseur de propriété

`objectName.propertyName`

Utilisez des accesseurs de propriété pour accéder aux propriétés d’un objet. Les accesseurs de propriété peuvent être utilisés avec n’importe quel objet, y compris des paramètres et des variables qui sont des objets. Si vous utilisez l’accès aux propriétés sur une expression qui n’est pas un objet, une erreur se produit.

### <a name="example"></a>Exemple

L’exemple suivant montre une variable objet et comment accéder aux propriétés.

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `outputZ` | string | 'Hello' |
| `outputQ` | entier | 42 |

En général, vous utilisez l’accesseur de propriété avec une ressource déployée dans le fichier Bicep. Dans l’exemple suivant, une adresse IP publique est créée et des accesseurs de propriété sont utilisés pour retourner une valeur à partir de la ressource déployée.

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="next-steps"></a>Étapes suivantes

- Pour exécuter les exemples, utilisez Azure CLI ou Azure PowerShell afin de [déployer un fichier Bicep](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).
- Pour créer un fichier Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Pour plus d’informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](./bicep-functions-any.md).
