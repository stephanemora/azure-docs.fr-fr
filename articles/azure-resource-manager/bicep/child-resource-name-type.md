---
title: Ressources enfants dans Bicep
description: Explique comment définir le nom et le type des ressources enfants dans Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: fbaf2eb104f213c6b4b071ce027b7cb6022aad97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244613"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Définir le nom et le type des ressources enfants dans Bicep

Les ressources enfants sont des ressources qui existent uniquement dans le contexte d’une autre ressource. Par exemple, une [extension de machine virtuelle](/azure/templates/microsoft.compute/virtualmachines/extensions) ne peut pas exister sans [machine virtuelle](/azure/templates/microsoft.compute/virtualmachines). La ressource d’extension est un enfant de la machine virtuelle.

Chaque ressource parente accepte uniquement certains types de ressources comme ressources enfants. La hiérarchie des types de ressources est disponible dans la [référence de ressource Bicep](/azure/templates/).

Cet article montre différentes façons de déclarer une ressource enfant.

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les ressources enfants et pour obtenir des conseils pratiques, consultez [Déployer des ressources enfants et d’extension à l’aide de Bicep](/learn/modules/child-extension-bicep-templates) sur **Microsoft Learn**.

## <a name="name-and-type-pattern"></a>Modèle de nom et de type

Dans Bicep, vous pouvez spécifier la ressource enfant dans la ressource parente ou en dehors de celle-ci. Les valeurs que vous fournissez pour le nom de ressource et le type de ressource varient en fonction de la manière dont vous déclarez la ressource enfant. Toutefois, le nom complet et le type sont toujours résolus sur le même modèle. 

Le **nom complet** de la ressource enfant utilise le modèle :

```bicep
{parent-resource-name}/{child-resource-name}
```

Si vous avez plus de deux niveaux dans la hiérarchie, conservez les noms parents récurrents :

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

Le **type complet** de la ressource enfant utilise le modèle :

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

Si vous avez plus de deux niveaux dans la hiérarchie, conservez les noms parents récurrents :

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

Si vous comptez les segments entre les `/` caractères, le nombre de segments dans le type est toujours supérieur à un nombre de segments dans le nom. 

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

Lorsqu’elles sont définies dans le type de ressource parent, les valeurs de type et de nom sont formatées en un seul segment sans barres obliques. L’exemple suivant illustre un compte de stockage avec une ressource enfant pour le service de fichiers, et le service de fichiers a une ressource enfant pour le partage de fichiers. Le nom du service de fichier est défini sur `default` et son type est défini sur `fileServices` . Le nom du partage de fichiers est défini `exampleshare` et son type est défini sur `shares`.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

Les types de ressources complets sont toujours `Microsoft.Storage/storageAccounts/fileServices` et `Microsoft.Storage/storageAccounts/fileServices/shares`. Vous ne fournissez pas `Microsoft.Storage/storageAccounts/`, car il est déduit du type et de la version de la ressource parent. La ressource imbriquée peut éventuellement déclarer une version d’API en utilisant la syntaxe `<segment>@<version>`. Si la ressource imbriquée omet la version de l’API, la version d’API de la ressource parent est utilisée. Si la ressource imbriquée spécifie une version d’API, la version d’API spécifiée est utilisée.

Les noms des ressources enfants sont définis sur `default` et `exampleshare`, mais les noms complets incluent les noms des parents. Vous ne fournissez pas `examplestorage` ou `default` parce qu’ils sont considérés comme appartenant à la ressource parente.

Une ressource imbriquée peut accéder aux propriétés de sa ressource parent. D'autres ressources déclarées dans le corps de la même ressource parente peuvent se référer les unes aux autres en utilisant les noms symboliques. Une ressource parente ne peut pas accéder aux propriétés des ressources qu'elle contient, car cette tentative entraînerait une dépendance cyclique.

Pour référencer une ressource imbriquée en dehors de la ressource parente, celle-ci doit être qualifiée avec le nom de la ressource conteneur et l'opérateur `::`. Par exemple, pour générer une propriété à partir d’une ressource enfant :

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>En dehors de la ressource parent

L’exemple suivant montre la ressource enfant en dehors de la ressource parent. Vous pouvez utiliser cette approche si la ressource parent n'est pas déployée dans le même modèle ou si souhaitez utiliser une [boucle](loops.md) pour créer plusieurs ressources enfants. Spécifiez la propriété parente sur l'enfant avec la valeur définie sur le nom symbolique du parent. Avec cette syntaxe, vous devez toujours déclarer le type de ressource complet, mais le nom de la ressource enfant n'est que le nom de l'enfant.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

En cas de définition en dehors de la ressource parent, vous mettez en forme le type et ajouter des barres obliques pour inclure le type et le nom du parent.

L'exemple suivant illustre un compte de stockage, un service de fichiers et un partage de fichiers qui sont tous définis au niveau racine.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

Le référencement du nom symbolique de la ressource enfant fonctionne de la même manière que le référencement du parent.

## <a name="full-resource-name-outside-parent"></a>Nom complet de la ressource en dehors du parent

Vous pouvez également utiliser le nom et le type de la ressource complète lors de la déclaration de la ressource enfant en dehors du parent. Vous ne définissez pas la propriété parent sur la ressource enfant. Étant donné que la dépendance ne peut pas être déduite, vous devez la définir explicitement.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> La définition du nom de ressource complet et du type n’est pas l’approche recommandée. Ce n’est pas aussi sûr que d’utiliser l’une des autres approches.

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à créer des fichiers Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](./file.md).
* Pour en savoir plus sur le format du nom de la ressource lors du référencement de la ressource, consultez la [fonction de référence](./bicep-functions-resource.md#reference).
