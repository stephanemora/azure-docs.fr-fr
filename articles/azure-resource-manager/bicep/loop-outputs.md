---
title: Définir plusieurs instances d’une valeur de sortie dans Bicep
description: Utilisez une boucle de sortie Bicep pour itérer et retourner des valeurs de déploiement.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: cc931b7e0d65804892176a2965f87022a2becb7b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025830"
---
# <a name="output-iteration-in-bicep"></a>Itération de sortie dans Bicep

Cet article explique comment créer plusieurs valeurs pour une sortie dans votre fichier Bicep. Vous pouvez ajouter une boucle à la section `output` du fichier et retourner dynamiquement plusieurs éléments au cours du déploiement.

Il est également possible d’utiliser une boucle avec des [ressources](loop-resources.md), des [propriétés de ressource](loop-properties.md) et des [variables](loop-variables.md).

## <a name="syntax"></a>Syntaxe

Les boucles peuvent être utilisées de plusieurs façons pour retourner de nombreux éléments pendant le déploiement :

- Par une itération sur un tableau.

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Par une itération sur les éléments d’un tableau.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. Pour déployer des fichiers Bicep, installez la dernière version des [outils Bicep](install.md).

## <a name="output-iteration"></a>Itération de sortie

L’exemple suivant crée un nombre variable de comptes de stockage et retourne un point de terminaison pour chacun d’eux.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): reference('${i}${baseNameVar}').primaryEndpoints.blob]
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Cet exemple retourne trois propriétés à partir des nouveaux comptes de stockage.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference('${i}${baseNameVar}', '2021-02-01', 'Full').resourceId
  blobEndpoint: reference('${i}${baseNameVar}').primaryEndpoints.blob
  status: reference('${i}${baseNameVar}').statusOfPrimary
}]
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

Cet exemple utilise un index de tableau, car les références directes à un module de ressource ou à une collection de modules ne sont pas prises en charge dans les boucles de sortie.

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
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations possibles des boucles, consultez :
  - [Itération de ressource dans les fichiers Bicep](loop-resources.md)
  - [Itération de propriété dans les fichiers Bicep](loop-properties.md)
  - [Itération de variable dans les fichiers Bicep](loop-variables.md)
- Pour plus d’informations sur les différentes sections d’un fichier Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
- Pour plus d’informations sur le déploiement de plusieurs ressources, consultez [Utiliser des modules Bicep](modules.md).
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
- Pour savoir comment effectuer un déploiement avec PowerShell, consultez [Déployer des ressources avec Bicep et Azure PowerShell](deploy-powershell.md).
- Pour savoir comment effectuer un déploiement avec Azure CLI, consultez [Déployer des ressources avec Bicep et Azure CLI](deploy-cli.md).
