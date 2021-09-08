---
title: Définir plusieurs instances d’une valeur de sortie dans Bicep
description: Utilisez une boucle de sortie Bicep pour itérer et retourner des valeurs de déploiement.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 67c4a99dbdb370202e4bbf080f32c626b71093b1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213841"
---
# <a name="output-iteration-in-bicep"></a>Itération de sortie dans Bicep

Cet article explique comment créer plusieurs valeurs pour une sortie dans votre fichier Bicep. Vous pouvez ajouter une boucle à la section `output` du fichier et retourner dynamiquement plusieurs éléments au cours du déploiement.

Il est également possible d’utiliser une boucle avec des [modules](loop-modules.md), des [ressources](loop-resources.md), des [propriétés dans une ressource](loop-properties.md) et des [variables](loop-variables.md).

## <a name="syntax"></a>Syntaxe

Les boucles peuvent être utilisées pour retourner des éléments pendant le déploiement :

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Pour plus d’informations, consultez [Index de boucle](#loop-index).

- Par une itération sur un tableau.

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Itération au sein d’un tableau et d’un index.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

  Pour plus d’informations, consultez [Tableau de boucles et index](#loop-array-and-index).

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. 

## <a name="loop-index"></a>Index de boucle

L’exemple suivant crée un nombre variable de comptes de stockage et retourne un point de terminaison pour chacun d’eux.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): stg[i].properties.primaryEndpoints.blob]
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

L’exemple suivant retourne trois propriétés à partir des nouveaux comptes de stockage.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: stg[i].id
  blobEndpoint: stg[i].properties.primaryEndpoints.blob
  status: stg[i].properties.statusOfPrimary
}]
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="loop-array-and-index"></a>Tableau de boucles et index

Cet exemple utilise à la fois les éléments d’un tableau et un index.

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations possibles des boucles, consultez :
  - [Itération de ressource dans Bicep](loop-resources.md)
  - [Itération de module dans Bicep](loop-modules.md)
  - [Itération de propriété dans Bicep](loop-properties.md)
  - [Itération de variable dans Bicep](loop-variables.md)
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).

