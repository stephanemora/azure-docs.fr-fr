---
title: Déclarer des ressources dans Bicep
description: Explique comment déclarer des ressources en vue de les déployer dans Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f62c790f1cb4f0613e17d2bbb3e4fc13e39d8e39
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963464"
---
# <a name="resource-declaration-in-bicep"></a>Déclaration de ressources dans Bicep

Pour déployer une ressource via un fichier Bicep, vous devez ajouter une déclaration de ressource à l’aide du mot clé `resource`.

## <a name="set-resource-type-and-version"></a>Définir le type et la version d’une ressource

Lorsque vous ajoutez une ressource à votre fichier Bicep, commencez par définir le type de la ressource et la version de l’API. Ces valeurs déterminent les autres propriétés disponibles pour la ressource.

L’exemple suivant montre comment définir le type de ressource et la version de l’API pour un compte de stockage. L’exemple n’affiche pas la déclaration de ressource complète.

```bicep
resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

Vous définissez un nom symbolique pour la ressource. Dans l’exemple précédent, le nom symbolique est `myStorageAccount` . Vous pouvez utiliser n’importe quelle valeur pour le nom symbolique, mais elle ne peut pas être celle d’une autre ressource, d’un paramètre ou d’une variable du fichier Bicep. Le nom symbolique n’est pas le même que le nom de la ressource. Vous utiliserez le nom symbolique pour référencer facilement la ressource dans d’autres parties de votre fichier Bicep.

Bicep ne prend pas en charge `apiProfile`, qui est disponible dans le [JSON des modèles Azure Resource Manager (modèles ARM)](../templates/syntax.md).

## <a name="set-resource-name"></a>Définit un nom de ressource

Chaque ressource a un nom. Lorsque vous définissez le nom de la ressource, soyez attentif aux [règles et restrictions applicables aux noms de ressources](../management/resource-name-rules.md).

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

En règle générale, vous définissez le nom sur un paramètre afin de passer des valeurs différentes lors du déploiement.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="set-location"></a>Définissez un emplacement

De nombreuses ressources nécessitent un emplacement. Vous pouvez déterminer si la ressource a besoin d’un emplacement via IntelliSense ou une [référence de modèle](/azure/templates/). L’exemple suivant ajoute un paramètre d’emplacement utilisé pour le compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

En règle générale, vous définissez l’emplacement sur un paramètre afin de pouvoir le déployer à différents emplacements.

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

Différents types de ressources sont pris en charge à différents emplacements. Pour connaître les régions pris en charge pour un service Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/).  Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez Azure PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="set-tags"></a>Définir des étiquettes

Vous pouvez appliquer des étiquettes à une ressource pendant le déploiement. Les étiquettes vous aident à organiser logiquement vos ressources déployées. Pour obtenir des exemples de différentes façons de spécifier les étiquettes, consultez [Étiquettes de modèle ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Définir des propriétés spécifiques d’une ressource

Les propriétés précédentes sont génériques pour la plupart des types de ressources. Après avoir défini ces valeurs, vous devez définir les propriétés qui sont spécifiques du type de ressource que vous déployez.

Utilisez IntelliSense ou une [référence de modèle](/azure/templates/) pour déterminer les propriétés disponibles et celles qui sont requises. L’exemple suivant définit les propriétés restantes pour un compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="set-resource-dependencies"></a>Définir des dépendances de ressources

Quand vous déployez des ressources, vous devez éventuellement vous assurer que certaines ressources existent au préalable avant d’autres ressources. Par exemple, vous avez besoin d’un serveur SQL logique avant de déployer une base de données. Vous établissez cette relation en marquant une seule ressource (.resource) comme dépendante de l’autre ressource. L’ordre de déploiement des ressources peut être influencé de deux manières : une [dépendance implicite](#implicit-dependency) et une [dépendance explicite](#explicit-dependency)

Azure Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Vous devez uniquement définir des dépendances pour les ressources qui sont déployées dans un même fichier Bicep.

### <a name="implicit-dependency"></a>Dépendance implicite

Une dépendance implicite est créée lorsqu’une déclaration de ressource référence l’identificateur d’une autre déclaration de ressource dans une expression. Par exemple, dans l’exemple suivant, *dnsZone* est référencé par la deuxième définition de ressource :

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

Une ressource imbriquée a également une dépendance implicite à la ressource qui la contient.

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants dans Bicep](./child-resource-name-type.md).

### <a name="explicit-dependency"></a>Dépendance explicite

Une dépendance explicite est déclarée via la propriété `dependsOn` dans la déclaration de ressource. La propriété accepte un tableau d’identificateurs de ressources. Voici un exemple d’une zone DNS dépendant d’une autre zone DNS de manière explicite :

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

Vous pouvez être tenté d’utiliser `dependsOn` pour mapper les relations entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites. Par exemple, pour documenter la manière dont les ressources sont liées entre elles, `dependsOn` n’est pas la bonne approche. Vous ne pouvez pas lancer de requête pour savoir quelles ressources ont été définies dans l’élément `dependsOn` après le déploiement. La définition de dépendances inutiles ralentit le temps de déploiement, Resource Manager ne pouvant pas déployer ces ressources en parallèle.

Les dépendances explicites sont rarement nécessaires. Dans la plupart des cas, vous disposez d’une référence symbolique qui permet de supposer la dépendance entre les ressources. Si vous vous retrouvez à utiliser dependsOn, essayez de voir si vous pouvez le remplacer par autre chose.

### <a name="visualize-dependencies"></a>Visualiser les dépendances

Visual Studio Code fournit un outil qui permet de visualiser les dépendances. Ouvrez un fichier Bicep dans Visual Studio Code, puis sélectionnez le bouton du visualiseur dans le coin supérieur gauche.  La capture d’écran suivante montre les dépendances d’une ressource de machine virtuelle qui sont définies dans le fichier Bicep.

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Capture d’écran du visualiseur de ressources Bicep dans Visual Studio Code":::

## <a name="reference-existing-resources"></a>Référencer des ressources existantes

Vous pouvez ajouter des références et accéder aux propriétés d’exécution de ressources situées en dehors du fichier actuel en utilisant le mot clé `existing` dans une déclaration de ressource. Cela équivaut à utiliser la [fonction reference()](../templates/template-functions-resource.md#reference) des modèles ARM.

Lorsque vous utilisez le mot clé `existing`, vous devez fournir le nom (`name`) de la ressource et éventuellement définir la propriété `scope` pour accéder à une ressource située dans une autre étendue. Pour plus d’informations sur l’utilisation de la propriété Scope, consultez [Étendues des ressources](./deploy-to-resource-group.md).

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'exampleStorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

L’exemple précédent ne déploie pas le compte de stockage, mais la déclaration fournit l’accès aux propriétés de la ressource existante. À l’aide du nom symbolique « stg », vous pouvez accéder aux propriétés du compte de stockage.

Les exemples suivants montrent comment spécifier la propriété `scope` :

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = { name: 'exampleStorage' scope: resourceGroup(mySub, myRg) }

## <a name="next-steps"></a>Étapes suivantes

- Pour déployer une ressource de manière conditionnelle, consultez [Déploiement conditionnel dans Bicep](./conditional-resource-deployment.md).