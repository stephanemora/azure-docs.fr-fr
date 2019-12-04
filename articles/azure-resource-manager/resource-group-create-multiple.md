---
title: Déployer plusieurs instances de ressources
description: Utilisez l’opération de copie et les tableaux dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors du déploiement de ressources.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 3ee4b47dd6cb9043a4100d114c483d1feadbde38
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150789"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Itération de variable, de propriété ou de ressource dans les modèles Azure Resource Manager

Cet article explique comment créer plusieurs instances d’une ressource, d’une variable ou d’une propriété dans votre modèle Azure Resource Manager. Pour créer plusieurs instances, ajoutez l’objet `copy` à votre modèle.

Lorsqu’il est utilisé avec une ressource, l’objet de la copie a le format suivant :

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Lorsqu’il est utilisé avec une variable ou une propriété, l’objet de la copie a le format suivant :

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Les deux formats sont décrits de façon plus détaillée dans cet article. Pour un didacticiel, consultez [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

Si vous devez spécifier si une ressource est déployée, consultez la page relative à l’[élément Condition](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Limites de copie

Pour spécifier le nombre d’itérations, vous devez fournir une valeur pour la propriété count. Le nombre ne peut pas dépasser 800.

Le nombre ne peut pas être négatif. Si vous déployez un modèle avec Azure PowerShell 2.6 (ou une version ultérieure), l’interface Azure CLI 2.0.74 (ou une version ultérieure) ou l’API REST version **2019-05-10** (ou une version ultérieure), vous pouvez définir le nombre sur zéro. Les versions antérieures de PowerShell, de l’interface CLI et de l’API REST ne prennent pas en charge le nombre zéro.

Soyez prudent lorsque vous utilisez le [déploiement en mode Complet](deployment-modes.md) avec des copies. Si vous redéployez dans un groupe de ressources en mode Complet, toutes les ressources qui ne sont pas spécifiées dans le modèle après la résolution de la boucle de copie sont supprimées.

Les limites pour le nombre sont les mêmes, qu’il soit utilisé pour une ressource, une variable ou une propriété.

## <a name="resource-iteration"></a>Itération de ressource

Si vous souhaitez créer plusieurs instances d’une ressource dans un déploiement, ajoutez un élément `copy` au type de ressource. Dans l’élément copy, indiquez le nombre d’itérations et un nom pour cette boucle.

La ressource à créer plusieurs fois est au format suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

Notez que le nom de chaque ressource inclut la fonction `copyIndex()`, qui renvoie l’itération actuelle de la boucle. `copyIndex()` est basé sur zéro. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex())]",
```

Crée les noms suivants :

* storage0
* storage1
* storage2.

Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Le nombre d’itérations est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crée les noms suivants :

* storage1
* storage2
* storage3

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau. Si bien que l’exemple suivant :

```json
"parameters": {
  "org": {
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
    "name": "[concat('storage', parameters('org')[copyIndex()])]",
    "copy": {
      "name": "storagecopy",
      "count": "[length(parameters('org'))]"
    },
    ...
  }
]
```

Crée les noms suivants :

* storagecontoso
* storagefabrikam
* storagecoho

Par défaut, Resource Manager crée les ressources en parallèle. Il n’applique aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le modèle. L’ordre de création n’est pas garanti.

Toutefois, vous souhaiterez peut-être spécifier que les ressources soient déployées en séquence. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné. Pour déployer en série plusieurs instances d’une ressource, affectez à `mode` la valeur **serial** et à `batchSize` le nombre d’instances à déployer à la fois. Avec le mode série, Resource Manager crée une dépendance sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

Par exemple, pour déployer en série des comptes de stockage deux à la fois, utilisez :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

La propriété mode accepte également **parallel**, qui est la valeur par défaut.

Pour plus d’informations sur l’utilisation de l’élément copy avec les modèles imbriqués, consultez [Utilisation de l’élément copy](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Itération de propriété

Pour créer plusieurs valeurs pour une propriété sur une ressource, ajoutez un tableau `copy` dans l’élément properties. Ce tableau contient des objets possédant tous les propriétés suivantes :

* name : nom de la propriété pour laquelle plusieurs valeurs seront créées
* count : nombre de valeurs à créer.
* input : objet contenant les valeurs à assigner à la propriété

L’exemple suivant montre comment appliquer `copy` à la propriété dataDisks sur une machine virtuelle :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Notez que, lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de propriété, vous devez fournir le nom de l’itération. Il est inutile de fournir le nom lorsque l’itération de propriété est utilisée avec une itération de ressource.

Le Gestionnaire des ressources développe le tableau `copy` durant le déploiement. Le nom du tableau devient celui de la propriété. Les valeurs d’entrée deviennent les propriétés de l’objet. Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

L'élément copy est un tableau. Vous pouvez donc spécifier plusieurs propriétés pour la ressource. Ajoutez un objet à chaque propriété à créer.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Vous pouvez utiliser des itérations de ressource et de propriété ensemble. Référencez l’itération de propriété par son nom.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>Itération de variable

Pour créer plusieurs instances d’une variable, utilisez la propriété `copy` dans la section des variables. Vous créez un tableau d’éléments construits à partir de la valeur de la propriété `input`. Vous pouvez utiliser la propriété `copy` au sein d’une variable, ou au niveau supérieur de la section des variables. Lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de variable, vous devez fournir le nom de l’itération.

Pour obtenir un exemple simple de création d’un tableau de valeurs de chaîne, consultez [Copier le modèle de tableau](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

L’exemple suivant montre plusieurs façons différentes de créer des variables de tableau avec des éléments construits dynamiquement. Il montre comment utiliser la copie à l’intérieur d’une variable pour créer des tableaux d’objets et de chaînes. Il montre également comment utiliser la copie au niveau supérieur pour créer des tableaux d’objets, de chaînes et d’entiers.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

Le type de variable créé dépend de l’objet d’entrée. Par exemple, la variable nommée **top-level-object-array** dans l’exemple précédent retourne :

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

Et la variable nommée **top-level-string-array** retourne :

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle

Vous spécifiez qu’une ressource est déployée après une autre ressource à l’aide de l’élément `dependsOn`. Pour déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément dependsOn. L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le nom de l’élément de copie a la valeur `storagecopy` et que l’élément dependsOn pour la machine virtuelle est également défini sur `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Itération d’une ressource enfant
Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que l’on définit en général comme imbriquée dans une autre ressource, vous devez au contraire la créer sous la forme d’une ressource de premier niveau. Vous définissez la relation avec la ressource parente par le biais des propriétés type et name.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
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

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
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

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants montrent des scénarios courants de création de plusieurs instances d’une ressource ou d’une propriété.

|Modèle  |Description  |
|---------|---------|
|[Copie de stockage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Déploie plusieurs comptes de stockage dont le nom comporte un numéro d’index . |
|[Copie de stockage en série](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Déploie plusieurs comptes de stockage un par un. Le nom inclut le numéro d’index. |
|[Copie de stockage avec tableau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Déploie plusieurs comptes de stockage. Le nom contient une valeur tirée d’un tableau. |
|[Déploiement de machine virtuelle avec un nombre variable de disques de données](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Déploie plusieurs disques de données avec une machine virtuelle. |
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Illustre les différentes façons d’itérer sur des variables. |
|[Règles de sécurité multiples](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Déploie plusieurs règles de sécurité sur un groupe de sécurité réseau. Crée les règles de sécurité à partir d’un paramètre. Pour le paramètre, consultez [plusieurs fichiers de paramètre NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Étapes suivantes

* Pour effectuer un didacticiel, consultez [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

