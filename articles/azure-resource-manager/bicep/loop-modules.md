---
title: Déployer plusieurs instances de modules dans Bicep
description: Utilisez des boucles et des tableaux dans un fichier Bicep pour déployer plusieurs instances de modules.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 44e5a4356cac0f252b344a7acf8b68a2f51d1ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215242"
---
# <a name="module-iteration-in-bicep"></a>Itération de module dans Bicep

Cet article explique comment déployer plusieurs instances d’un [module](modules.md) dans votre fichier Bicep. Vous pouvez ajouter une boucle à une déclaration `module` et définir de manière dynamique le nombre de fois où il faut déployer ce module. Vous évitez de répéter la syntaxe dans votre fichier Bicep.

Vous pouvez également utiliser une boucle avec les éléments [resources](loop-resources.md), [properties](loop-properties.md), [variables](loop-variables.md) et [outputs](loop-outputs.md).

## <a name="syntax"></a>Syntaxe

Vous pouvez utiliser des boucles pour déclarer plusieurs modules en procédant comme suit :

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  Pour plus d’informations, consultez [Index de boucle](#loop-index).

- Par une itération sur un tableau.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  Pour plus d’informations, consultez [Tableau de boucle](#loop-array).

- Itération sur un tableau et un index :

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations.

## <a name="loop-index"></a>Index de boucle

L’exemple suivant déploie un module le nombre de fois spécifié dans le paramètre `storageCount`. Chaque instance du module crée un compte de stockage.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Notez que l’index `i` est utilisé pour créer le nom de ressource du compte de stockage. Le compte de stockage est transmis en tant que valeur de paramètre au module.

## <a name="loop-array"></a>Tableau de boucle

L’exemple suivant déploie un module pour chaque nom fourni dans le paramètre `storageNames`. Le module crée un compte de stockage

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

Le référencement d’une collection de modules n’est pas pris en charge dans les boucles de sortie. Pour afficher les résultats des modules dans une collection, appliquez un indexeur de tableau à l’expression. Pour plus d’informations, consultez [Itération de sortie](loop-outputs.md).

## <a name="module-iteration-with-condition"></a>Itération de module avec condition

L’exemple suivant montre un fichier Bicep avec une boucle de module filtrée. Les filtres doivent être des expressions qui correspondent à une valeur booléenne.

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Dans l’exemple précédent, le module est appelé uniquement lorsque la valeur booléenne est `true`.

Les filtres sont également pris en charge avec les [boucles de ressource](loop-resources.md).

## <a name="deploy-in-batches"></a>Déployer par lots

Par défaut, Resource Manager crée des ressources en parallèle. Lorsque vous utilisez une boucle pour créer plusieurs instances d’un type de ressource, ces instances sont toutes déployées en même temps. L’ordre de création n’est pas garanti. Il n’existe aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le fichier Bicep.

Il se peut que vous ne souhaitiez pas mettre à jour toutes les instances d’un type de ressource en même temps. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné. Vous pouvez spécifier qu’un sous-ensemble des instances soit traité par lots ensemble et déployé en même temps. Les autres instances attendent que ce lot soit finalisé.

Pour déployer en série des instances d’un module, ajoutez l’[élément décoratif BatchSize](./file.md#resource-and-module-decorators). Définissez sa valeur comme le nombre d’instances à déployer simultanément. Une dépendance est créée sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Pour un déploiement purement séquentiel, définissez la taille du lot sur 1.

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations de la boucle, consultez :
  - [Itération de ressource dans Bicep](loop-resources.md)
  - [Itération de propriété dans Bicep](loop-properties.md)
  - [Itération de variable dans Bicep](loop-variables.md)
  - [Itération de sortie dans Bicep](loop-outputs.md)
- Pour plus d'informations sur les modules, consultez [Utilisation des modules Bicep](modules.md).
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
