---
title: Déployer plusieurs instances de ressources dans Bicep
description: Utilisez des boucles et des tableaux dans un fichier Bicep pour déployer plusieurs instances de ressources.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: fadd3cfac94889a187409e95331190b7d7b98b73
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362992"
---
# <a name="resource-iteration-in-bicep"></a>Itération de ressource dans Bicep

Cet article explique comment créer plusieurs instances d’une ressource dans votre fichier Bicep. Vous pouvez ajouter une boucle à une déclaration `resource` et définir de manière dynamique le nombre de ressources à déployer. Vous évitez de répéter la syntaxe dans votre fichier Bicep.

Vous pouvez également utiliser une boucle avec les éléments [modules](loop-modules.md), [properties](loop-properties.md), [variables](loop-variables.md) et [outputs](loop-outputs.md).

Si vous devez spécifier si une ressource est déployée, consultez la page relative à l’[élément Condition](conditional-resource-deployment.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les boucles et pour obtenir des conseils pratiques, consultez [Créer des modèles Bicep flexibles en utilisant des conditions et des boucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) sur **Microsoft Learn**.

## <a name="syntax"></a>Syntaxe

Vous pouvez utiliser des boucles pour déclarer plusieurs ressources par :

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

  Pour plus d’informations, consultez [Index de boucle](#loop-index).

- Par une itération sur un tableau.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

  Pour plus d’informations, consultez [Tableau de boucle](#loop-array).

- Itération sur un tableau et un index.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

  Pour plus d’informations, consultez [Tableau de boucles et index](#loop-array-and-index).

## <a name="loop-limits"></a>Limites des boucles

La boucle Bicep présente les limitations suivantes :

- Impossible de faire une boucle sur une ressource avec des ressources enfants imbriquées. Vous devez remplacer les ressources enfants par des ressources de niveau supérieur.  Voir [Itération d’une ressource enfant](#iteration-for-a-child-resource).
- Impossible de faire une boucle sur plusieurs niveaux de propriétés. Voir [Itération de propriété dans Bicep](./loop-properties.md).
- Le nombre d’itérations de boucle ne peut être ni négatif, ni supérieur à 800.

## <a name="loop-index"></a>Index de boucle

L’exemple suivant crée le nombre de comptes de stockage spécifiés dans le paramètre `storageCount`.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Notez que l’index `i` est utilisé pour créer le nom de ressource du compte de stockage.

## <a name="loop-array"></a>Tableau de boucle

L’exemple suivant crée un compte de stockage par nom fourni dans le paramètre `storageNames`.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Si vous souhaitez renvoyer des valeurs à partir des ressources déployées, vous pouvez utiliser une boucle dans la [section outputs](loop-outputs.md).

## <a name="loop-array-and-index"></a>Tableau de boucles et index

L’exemple suivant utilise à la fois l’élément de tableau et la valeur d’index lors de la définition du compte de stockage.

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-02-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  properties: {
    supportsHttpsTrafficOnly: true
    accessTier: 'Hot'
    encryption: {
      keySource: 'Microsoft.Storage'
      services: {
        blob: {
          enabled: true
        }
        file: {
          enabled: true
        }
      }
    }
  }
  kind: 'StorageV2'
  sku: {
    name: config.sku
  }
}]
```

## <a name="resource-iteration-with-condition"></a>Itération de ressource avec condition

L’exemple suivant montre une boucle imbriquée associée à une boucle de ressource filtrée. Les filtres doivent être des expressions qui correspondent à une valeur booléenne.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

Les filtres sont également pris en charge avec les [boucles de module](loop-modules.md).

## <a name="deploy-in-batches"></a>Déployer par lots

Par défaut, Resource Manager crée des ressources en parallèle. Lorsque vous utilisez une boucle pour créer plusieurs instances d’un type de ressource, ces instances sont toutes déployées en même temps. L’ordre de création n’est pas garanti. Il n’existe aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le fichier Bicep.

Il se peut que vous ne souhaitiez pas mettre à jour toutes les instances d’un type de ressource en même temps. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné. Vous pouvez spécifier qu’un sous-ensemble des instances soit traité par lots ensemble et déployé en même temps. Les autres instances attendent que ce lot soit finalisé.

Pour déployer en série des instances d’une ressource, ajoutez l’[élément décoratif BatchSize](./file.md#resource-and-module-decorators). Définissez sa valeur comme le nombre d’instances à déployer simultanément. Une dépendance est créée sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Pour un déploiement purement séquentiel, définissez la taille du lot sur 1.

L’élément décoratif `batchSize` se trouve dans l’[espace de noms sys](bicep-functions.md#namespaces-for-functions). Si vous devez différencier cet élément décoratif d'un autre élément portant le même nom, faites précéder l’élément décoratif de **sys**: `@sys.batchSize(2)`

## <a name="iteration-for-a-child-resource"></a>Itération d’une ressource enfant

Vous ne pouvez pas utiliser une boucle pour une ressource enfant imbriquée. Pour créer plusieurs instances d’une ressource enfant, remplacez la ressource enfant par une ressource de niveau supérieur.

Supposons, par exemple, que vous définissiez habituellement un service de fichiers et un partage de fichiers en tant que ressources imbriquées pour un compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

Pour créer plusieurs partages de fichiers, déplacez-le en dehors du compte de stockage. Vous définissez la relation avec la ressource parente par le biais de la propriété `parent`.

L’exemple suivant montre comment créer un compte de stockage, un service de fichiers et plusieurs partages de fichiers :

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations de la boucle, consultez :
  - [Itération de propriété dans Bicep](loop-properties.md)
  - [Itération de variable dans Bicep](loop-variables.md)
  - [Itération de sortie dans Bicep](loop-outputs.md)
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
