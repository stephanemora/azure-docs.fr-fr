---
title: Définir plusieurs instances d’une variable dans Bicep
description: Utilisez une boucle de variable Bicep pour effectuer une itération lors de la création d’une variable.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 14721057379217ae9d14b97c94483435dde08203
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831989"
---
# <a name="variable-iteration-in-bicep"></a>Itération de variable dans Bicep

Cet article explique comment créer plusieurs valeurs pour une variable dans votre fichier Bicep. Vous pouvez ajouter une boucle à la déclaration `variables` et définir de manière dynamique le nombre d’éléments pour une variable. Vous évitez de répéter la syntaxe dans votre fichier Bicep.

Vous pouvez également utiliser l’élément copy avec des [modules](loop-modules.md), des [ressources](loop-resources.md), des [propriétés](loop-properties.md) dans une ressource et des [sorties](loop-outputs.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les boucles et pour obtenir des conseils pratiques, consultez [Créer des modèles Bicep flexibles en utilisant des conditions et des boucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) sur **Microsoft Learn**.

## <a name="syntax"></a>Syntaxe

Vous pouvez utiliser des boucles pour déclarer plusieurs variables en procédant comme suit :

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Pour plus d’informations, consultez [Index de boucle](#loop-index).

- Par une itération sur un tableau.

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  Pour plus d’informations, consultez [Tableau de boucle](#loop-array).

- Itération sur un tableau et un index.

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. 

## <a name="loop-index"></a>Index de boucle

L’exemple suivant montre comment créer un tableau de valeurs de chaîne :

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

L’exemple suivant montre comment créer un tableau d’objets avec trois propriétés : `name`, `diskSizeGB` et `diskIndex`.

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="loop-array"></a>Tableau de boucle

L’exemple suivant effectue une boucle sur un tableau qui est passé en tant que paramètre. La variable construit des objets dans le format requis à partir du paramètre.

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations possibles des boucles, consultez :
  - [Itération de ressource dans Bicep](loop-resources.md)
  - [Itération de module dans Bicep](loop-modules.md)
  - [Itération de propriété dans Bicep](loop-properties.md)
  - [Itération de sortie dans Bicep](loop-outputs.md)
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
