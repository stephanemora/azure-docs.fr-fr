---
title: Ressources enfants dans Bicep
description: Explique comment définir le nom et le type des ressources enfants dans Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: e19240a8050a7076a73c17f7e8ff3a9ab0d59591
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025895"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Définir le nom et le type des ressources enfants dans Bicep

Les ressources enfants sont des ressources qui existent uniquement dans le contexte d’une autre ressource. Par exemple, une [extension de machine virtuelle](/azure/templates/microsoft.compute/virtualmachines/extensions) ne peut pas exister sans [machine virtuelle](/azure/templates/microsoft.compute/virtualmachines). La ressource d’extension est un enfant de la machine virtuelle.

Chaque ressource parente accepte uniquement certains types de ressources comme ressources enfants. Le type de ressource de la ressource enfant comprend le type de ressource de la ressource parent. Par exemple, `Microsoft.Web/sites/config` et `Microsoft.Web/sites/extensions` sont des ressources enfant de la ressource `Microsoft.Web/sites`. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parente.

Dans Bicep, vous pouvez spécifier la ressource enfant dans la ressource parente ou en dehors de celle-ci. Les valeurs que vous fournissez pour le nom et le type de la ressource varient selon que la ressource enfant est définie dans la ressource parent ou en dehors de celle-ci.

## <a name="within-parent-resource"></a>Dans la ressource parent

L’exemple suivant illustre la ressource enfant incluse dans la propriété Ressources de la ressource parent.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

Une déclaration de ressource imbriquée doit apparaître au niveau supérieur de la syntaxe de la ressource parent. Les déclarations peuvent être imbriquées à une profondeur arbitraire, tant que chaque niveau est un type enfant de sa ressource parent.

Lorsqu’elles sont définies dans le type de ressource parent, les valeurs de type et de nom sont formatées en un seul segment sans barres obliques. L'exemple suivant illustre un compte de stockage avec un service de fichiers et un partage de fichiers. Le nom du service de fichiers est défini sur **default** et son type sur **fileServices**. Le nom du partage de fichiers est défini sur **exampleshare** et son type sur **shares**.

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
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

Les types de ressources complets sont toujours `Microsoft.Storage/storageAccounts/fileServices` et `Microsoft.Storage/storageAccounts/fileServices/shares`. Vous ne fournissez pas `Microsoft.Storage/storageAccounts/`, car il est déduit du type et de la version de la ressource parent. La ressource imbriquée peut éventuellement déclarer une version d’API en utilisant la syntaxe `<segment>@<version>`. Si la ressource imbriquée omet la version de l’API, la version d’API de la ressource parent est utilisée. Si la ressource imbriquée spécifie une version d’API, la version d’API spécifiée est utilisée.

Les noms des ressources enfants sont définis sur **default** et **exampleshare**, mais les noms complets incluent les noms des parents. Vous ne fournissez pas **examplestorage** ou **default** car ils sont supposés provenir de la ressource parente.

Une ressource imbriquée peut accéder aux propriétés de sa ressource parent. D'autres ressources déclarées dans le corps de la même ressource parente peuvent se référer les unes aux autres en utilisant les noms symboliques. Une ressource parente ne peut pas accéder aux propriétés des ressources qu'elle contient, car cette tentative entraînerait une dépendance cyclique.

Pour référencer une ressource imbriquée en dehors de la ressource parente, celle-ci doit être qualifiée avec le nom de la ressource conteneur et l'opérateur `::`. Par exemple, pour générer une propriété à partir d’une ressource enfant :

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>En dehors de la ressource parent

L’exemple suivant montre la ressource enfant en dehors de la ressource parent. Vous pouvez utiliser cette approche si la ressource parent n'est pas déployée dans le même modèle ou si souhaitez utiliser une [boucle](loop-resources.md) pour créer plusieurs ressources enfants. Spécifiez la propriété parente sur l'enfant avec la valeur définie sur le nom symbolique du parent. Avec cette syntaxe, vous devez toujours déclarer le type de ressource complet, mais le nom de la ressource enfant n'est que le nom de l'enfant.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: 'myParent'
  name: 'myChild'
  <child-resource-properties>
}
```

En cas de définition en dehors de la ressource parent, vous mettez en forme le type et ajouter des barres obliques pour inclure le type et le nom du parent.

L'exemple suivant illustre un compte de stockage, un service de fichiers et un partage de fichiers qui sont tous définis au niveau racine.

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: storage
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = {
  name: 'exampleshare'
  parent: service
}
```

Le référencement du nom symbolique de la ressource enfant fonctionne de la même manière que le référencement du parent.

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à créer des fichiers Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](./file.md).
* Pour en savoir plus sur le format du nom de la ressource lors du référencement de la ressource, consultez la [fonction de référence](./bicep-functions-resource.md#reference).
