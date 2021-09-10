---
title: Déployer plusieurs instances de ressources dans Bicep
description: Utilisez des boucles et des tableaux dans un fichier Bicep pour déployer plusieurs instances de ressources.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 3185d6bac1e20e1d29c4f55b0a4e954b5ae35499
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634882"
---
# <a name="resource-iteration-in-bicep"></a>Itération de ressource dans Bicep

Cet article explique comment créer plusieurs instances d’une ressource dans votre fichier Bicep. Vous pouvez ajouter une boucle à la section `resource` de votre fichier et définir dynamiquement le nombre de ressources à déployer. Vous évitez également de répéter la syntaxe dans votre fichier Bicep.

Vous pouvez également utiliser une boucle avec les éléments [properties](loop-properties.md), [variables](loop-variables.md) et [outputs](loop-outputs.md).

Si vous devez spécifier si une ressource est déployée, consultez la page relative à l’[élément Condition](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntaxe

Vous pouvez utiliser des boucles pour déclarer plusieurs ressources par :

- Par une itération sur un tableau.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Par une itération sur les éléments d’un tableau.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. Pour déployer des fichiers Bicep, installez la dernière version des [outils Bicep](install.md).

## <a name="resource-iteration"></a>Itération de ressource

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

Les filtres sont également pris en charge avec les boucles de module.

## <a name="deploy-in-batches"></a>Déployer par lots

Par défaut, Resource Manager crée des ressources en parallèle. Lorsque vous utilisez une boucle pour créer plusieurs instances d’un type de ressource, ces instances sont toutes déployées en même temps. L’ordre de création n’est pas garanti. Il n’existe aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le fichier Bicep.

Il se peut que vous ne souhaitiez pas mettre à jour toutes les instances d’un type de ressource en même temps. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné. Vous pouvez spécifier qu’un sous-ensemble des instances soit traité par lots ensemble et déployé en même temps. Les autres instances attendent que ce lot soit finalisé.

Pour déployer en série des instances d’une ressource, ajoutez l’[élément décoratif BatchSize](./file.md#resource-and-module-decorators). Définissez sa valeur sur le nombre d’instances à déployer à la fois. Une dépendance est créée sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

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

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants montrent des scénarios courants de création de plusieurs instances d’une ressource ou d’une propriété.

|Modèle  |Description  |
|---------|---------|
|[Stockage de boucle](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopstorage.bicep) |Déploie plusieurs comptes de stockage dont le nom comporte un numéro d’index . |
|[Stockage de boucle en série](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopserialstorage.bicep) |Déploie plusieurs comptes de stockage un par un. Le nom inclut le numéro d’index. |
|[Stockage de boucle avec tableau](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopstoragewitharray.bicep) |Déploie plusieurs comptes de stockage. Le nom contient une valeur tirée d’un tableau. |

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations de la boucle, consultez :
  - [Itération de propriété dans les fichiers Bicep](loop-properties.md)
  - [Itération de variable dans les fichiers Bicep](loop-variables.md)
  - [Itération de sortie dans les fichiers Bicep](loop-outputs.md)
- Pour plus d’informations sur les différentes sections d’un fichier Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
- Pour plus d’informations sur le déploiement de plusieurs ressources, consultez [Utiliser des modules Bicep](modules.md).
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
- Pour savoir comment effectuer un déploiement avec PowerShell, consultez [Déployer des ressources avec Bicep et Azure PowerShell](deploy-powershell.md).
- Pour savoir comment effectuer un déploiement avec Azure CLI, consultez [Déployer des ressources avec Bicep et Azure CLI](deploy-cli.md).
