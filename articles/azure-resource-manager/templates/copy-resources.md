---
title: Déployer plusieurs instances de ressources
description: Utilisez l’opération copy et les tableaux dans un modèle Azure Resource Manager (modèle ARM) pour déployer un même type de ressource plusieurs fois.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 3af676cce544c125e441857f06556b9ff7eee697
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385704"
---
# <a name="resource-iteration-in-arm-templates"></a>Itération de ressource dans les modèles ARM

Cet article explique comment créer plusieurs instances d’une ressource dans votre modèle Azure Resource Manager (modèle ARM). En ajoutant copy loop à la section resources de votre modèle, vous pouvez définir de façon dynamique le nombre de ressources à déployer. Cela vous évite également de répéter la syntaxe du modèle.

Vous pouvez également utiliser copy loop avec les éléments [properties](copy-properties.md), [variables](copy-variables.md) et [outputs](copy-outputs.md).

Si vous devez spécifier si une ressource est déployée, consultez la page relative à l’[élément Condition](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntaxe

# <a name="json"></a>[JSON](#tab/json)

Ajoutez l’élément `copy` à la section resources de votre modèle pour déployer plusieurs instances de la ressource. L’élément `copy` utilise le format général suivant :

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

La propriété `name` est toute valeur qui identifie la boucle. La propriété `count` spécifie le nombre d’itérations que vous souhaitez pour le type de ressource.

Utilisez les propriétés `mode` et `batchSize` pour spécifier si les ressources sont déployées en parallèle ou en séquence. Ces propriétés sont décrites dans [Série ou parallèle](#serial-or-parallel).

# <a name="bicep"></a>[Bicep](#tab/bicep)

Vous pouvez utiliser des boucles pour déclarer plusieurs ressources par :

- Itération sur un tableau :

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }
  ```

- Itération sur les éléments d’un tableau

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }
  ```

- Utilisation de l’index de boucle

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }
  ```

---

## <a name="copy-limits"></a>Limites de copie

Le nombre ne peut pas dépasser 800.

Le nombre ne peut pas être négatif. Il peut être zéro si vous déployez le modèle avec une version récente d’Azure CLI, de PowerShell ou de l’API REST. Plus précisément, vous devez utiliser :

- Azure PowerShell **2.6** ou version ultérieure
- Azure CLI **2.0.74** ou version ultérieure
- API REST version **2019-05-10** ou ultérieure
- Les [déploiements liés](linked-templates.md) doivent utiliser la version **2019-05-10** de l’API ou une version ultérieure pour le type de ressource de déploiement

Les versions antérieures de PowerShell, de l’interface CLI et de l’API REST ne prennent pas en charge le nombre zéro.

Soyez prudent lorsque vous utilisez un [déploiement en mode complet](deployment-modes.md) avec un boucle de copie. Si vous redéployez dans un groupe de ressources en mode Complet, toutes les ressources qui ne sont pas spécifiées dans le modèle après la résolution de la boucle de copie sont supprimées.

## <a name="resource-iteration"></a>Itération de ressource

L’exemple suivant crée le nombre de comptes de stockage spécifiés dans le paramètre `storageCount`.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Notez que le nom de chaque ressource inclut la fonction `copyIndex()`, qui renvoie l’itération actuelle de la boucle. `copyIndex()` est basé sur zéro. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex())]",
```

Crée les noms suivants :

- storage0
- storage1
- storage2.

Pour décaler la valeur d’index, vous pouvez passer une valeur dans la fonction `copyIndex()`. Le nombre d’itérations est toujours spécifié dans l’élément copy, mais la valeur de `copyIndex` est décalée en fonction de la valeur spécifiée. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crée les noms suivants :

- storage1
- storage2
- storage3

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Notez que l’index `i` est utilisé pour créer le nom de ressource du compte de stockage.

---

L’exemple suivant crée un compte de stockage par nom fourni dans le paramètre.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Si vous souhaitez retourner des valeurs à partir des ressources déployées, vous pouvez utiliser l’[élément copy dans la section outputs](copy-outputs.md).

## <a name="serial-or-parallel"></a>Série ou parallèle

Par défaut, Resource Manager crée les ressources en parallèle. Il n’applique aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le modèle. L’ordre de création n’est pas garanti.

Toutefois, vous souhaiterez peut-être spécifier que les ressources soient déployées en séquence. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné.

Par exemple, pour déployer en série des comptes de stockage deux à la fois, utilisez :

# <a name="json"></a>[JSON](#tab/json)

Pour déployer en série plusieurs instances d’une ressource, affectez à `mode` la valeur **serial** et à `batchSize` le nombre d’instances à déployer à la fois. Avec le mode série, Resource Manager crée une dépendance sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

La valeur de `batchSize` ne peut pas dépasser la valeur de `count` dans l’élément copy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

La propriété `mode` accepte également **parallel**, qui est la valeur par défaut.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pour déployer en série plusieurs instances d’une ressource, définissez la valeur `batchSize` [decorator](./bicep-file.md#resource-and-module-decorators) sur le nombre d’instances à déployer à la fois. Avec le mode série, Resource Manager crée une dépendance sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Itération d’une ressource enfant

Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que l’on définit en général comme imbriquée dans une autre ressource, vous devez au contraire la créer sous la forme d’une ressource de premier niveau. Vous définissez la relation avec la ressource parente par le biais des propriétés type et name.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Pour créer plusieurs jeux de données, déplacez-le en dehors de la fabrique de données. Le jeu de données doit être au même niveau que la fabrique de données, mais il est toujours une ressource enfant de la fabrique de données. Vous conservez la relation entre le jeu de données et la fabrique de données par le biais des propriétés type et name. Étant donné que le type ne peut plus peut être déduit à partir de sa position dans le modèle, vous devez fournir le type qualifié complet au format : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pour établir une relation parent/enfant avec une instance de la fabrique de données, fournissez un nom pour le jeu de données incluant le nom de la ressource parente. Utilisez le format : `{parent-resource-name}/{child-resource-name}`.

L’exemple ci-après illustre l’implémentation :

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants montrent des scénarios courants de création de plusieurs instances d’une ressource ou d’une propriété.

|Modèle  |Description  |
|---------|---------|
|[Copie de stockage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Déploie plusieurs comptes de stockage dont le nom comporte un numéro d’index . |
|[Copie de stockage en série](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Déploie plusieurs comptes de stockage un par un. Le nom inclut le numéro d’index. |
|[Copie de stockage avec tableau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Déploie plusieurs comptes de stockage. Le nom contient une valeur tirée d’un tableau. |

## <a name="next-steps"></a>Étapes suivantes

- Pour définir des dépendances sur les ressources créées dans une boucle de copie, consultez [Définir l’ordre de déploiement des ressources dans les modèles ARM](define-resource-dependency.md).
- Pour suivre un tutoriel, consultez [Tutoriel : Créer plusieurs instances de ressources grâce à des modèles ARM](template-tutorial-create-multiple-instances.md).
- Pour lire un module Microsoft Learn qui aborde la copie des ressources, consultez [Gérer des déploiements cloud complexes à l’aide des fonctionnalités avancées de modèle ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Pour connaître les autres utilisations de la boucle de copie, consultez :
  - [Itération de propriété dans les modèles ARM](copy-properties.md)
  - [Itération de variable dans les modèles ARM](copy-variables.md)
  - [Itération de sortie dans les modèles ARM](copy-outputs.md)
- Pour plus d’informations sur l’utilisation de l’élément copy avec les modèles imbriqués, consultez [Utilisation de l’élément copy](linked-templates.md#using-copy).
