---
title: Cas de test de modèle pour le kit de ressources de test
description: Décrit les tests de modèle exécutés par le kit de ressources de test de modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532467"
---
# <a name="test-cases-for-arm-templates"></a>Cas de test pour les modèles ARM

Cet article décrit les tests exécutés avec le [kit de ressources de test de modèle](test-toolkit.md) pour les modèles ARM (Azure Resource Manager). Il fournit des exemples avec des tests qui **réussissent** ou qui **échouent** et inclut le nom de chaque test. Pour plus d’informations sur l’exécution de tests ou sur l’exécution d’un test spécifique, consultez [Paramètres de test](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Utiliser le schéma correct

Nom du test : **Le schéma DeploymentTemplate est correct**

Dans votre modèle, vous devez spécifier une valeur de schéma valide.

Dans l’exemple suivant, le test **échoue**, car le schéma n’est pas valide.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

L’exemple suivant affiche un **avertissement**, car la version de schéma `2015-01-01` est dépréciée et n’est plus gérée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

Dans l’exemple suivant, le test **réussit** car il utilise un schéma valide.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

La propriété `schema` du modèle doit être définie sur l’un des schémas suivants :

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>Les paramètres déclarés doivent être utilisés

Nom du test : **Les paramètres doivent être référencés**

Ce test recherche les paramètres qui ne sont pas utilisés dans le modèle ou qui ne sont pas utilisés dans une expression valide.

Pour améliorer la lisibilité de votre modèle, supprimez tous les paramètres qui sont définis mais qui ne sont pas utilisés. L’élimination des paramètres inutilisés simplifie les déploiements de modèle, car vous n’avez pas besoin de fournir des valeurs qui ne sont pas nécessaires.

Dans l’exemple suivant, le test **échoue**, car l’expression qui référence un paramètre ne contient pas le crochet ouvrant (`[`).

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

Dans l’exemple suivant, le test **réussit**, car l’expression est valide.

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>Les paramètres sécurisés ne peuvent pas avoir de valeur par défaut codée en dur

Nom du test : **Les paramètres de chaîne sécurisée ne peuvent pas avoir de valeur par défaut**

Ne fournissez pas de valeur par défaut codée en dur pour un paramètre sécurisé dans votre modèle. Un paramètre sécurisé peut avoir une chaîne vide comme valeur par défaut ou utiliser la fonction [newGuid](template-functions-string.md#newguid) dans une expression.

Vous utilisez les types `secureString` ou `secureObject` sur les paramètres qui contiennent des valeurs sensibles comme des mots de passe. Lorsqu’un paramètre utilise un type sécurisé, sa valeur n’est pas enregistrée ou stockée dans l’historique de déploiement. Cette action empêche un utilisateur malveillant de découvrir la valeur sensible.

Quand vous fournissez une valeur par défaut, cette valeur peut être découverte par toute personne ayant accès au modèle ou à l’historique de déploiement.

Dans l’exemple suivant, le test **échoue**.

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

Dans l’exemple suivant, le test **réussit**, car la fonction `newGuid` est utilisée.

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>Les URL d’environnement ne peuvent pas être codées en dur

Nom du test : **DeploymentTemplate ne doit pas contenir d’URI codé en dur**

Ne codez pas en dur les URL d’environnement dans votre modèle. Au lieu de cela, utilisez la fonction [environment](template-functions-deployment.md#environment) pour obtenir dynamiquement ces URL pendant le déploiement. Pour obtenir la liste des hôtes d’URL bloqués, consultez le [cas de test](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Dans l’exemple suivant, le test **échoue**, car l’URL est codée en dur.

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

De même, le test **échoue** quand il est utilisé avec [concat](template-functions-string.md#concat) ou [uri](template-functions-string.md#uri).

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>L’emplacement utilise le paramètre

Nom du test : **L’emplacement ne doit pas être codé en dur**

Pour la définition de l’emplacement d’une ressource, vos modèles doivent avoir un paramètre nommé `location` avec le type défini sur `string`. Dans le modèle principal, _azuredeploy.json_ ou _mainTemplate.json_, ce paramètre peut correspondre par défaut à l’emplacement du groupe de ressources. Dans les modèles liés ou imbriqués, le paramètre d'emplacement ne doit pas comporter d'emplacement par défaut.

Les utilisateurs de modèle peuvent avoir un accès limité aux régions où ils peuvent créer des ressources. Un emplacement de ressource codé en dur peut empêcher les utilisateurs de créer une ressource. L’expression `"[resourceGroup().location]"` peut bloquer les utilisateurs si le groupe de ressources a été créé dans une région à laquelle l’utilisateur ne peut pas accéder. Les utilisateurs bloqués ne peuvent pas utiliser le modèle.

En fournissant un paramètre `location` qui correspond par défaut à l’emplacement du groupe de ressources, les utilisateurs peuvent utiliser la valeur par défaut quand cela leur convient, mais aussi spécifier un autre emplacement.

Dans l’exemple suivant, le test **échoue**, car le paramètre `location` de la ressource est défini sur `resourceGroup().location`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Le prochain exemple utilise un paramètre `location`, mais le test **échoue**, car le paramètre est défini par défaut sur un emplacement codé en dur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

Dans l’exemple suivant, le test **réussit** quand le modèle est utilisé comme modèle principal. Créez un paramètre qui a pour valeur par défaut l’emplacement du groupe de ressources, mais qui permet aux utilisateurs de fournir une valeur différente.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> Si l’exemple précédent est utilisé comme modèle lié, le test **échoue**. Lorsqu'il est utilisé comme modèle lié, supprimez la valeur par défaut.

## <a name="resources-should-have-location"></a>Les ressources doivent avoir un emplacement

Nom du test : **Les ressources doivent avoir un emplacement**

L’emplacement d’une ressource doit être défini sur une [expression de modèle](template-expressions.md) ou `global`. L’expression de modèle utilise généralement le paramètre `location` décrit dans la section [L’emplacement utilise le paramètre](#location-uses-parameter).

Dans l’exemple suivant, le test **échoue**, car le paramètre `location` n’est pas une expression ou `global`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

Dans l’exemple suivant, le test **réussit**, car le paramètre `location` de la ressource est défini sur `global`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

Dans l’exemple suivant, le test **réussit** également, car le paramètre `location` utilise une expression. Le paramètre `location` de la ressource utilise la valeur de l’expression.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>La taille de machine virtuelle utilise un paramètre

Nom du test : **La taille de la machine virtuelle doit être un paramètre**

Ne codez pas en dur le paramètre `vmSize` de l’objet `hardwareProfile`. Le test échoue quand l’objet `hardwareProfile` est omis ou contient une valeur codée en dur. Fournissez un paramètre afin que les utilisateurs de votre modèle puissent modifier la taille de la machine virtuelle déployée. Pour plus d’informations, consultez [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines).

Dans l’exemple suivant, le test **échoue**, car le paramètre `vmSize` de l’objet `hardwareProfile` est codé en dur.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

Dans cet exemple, le test **réussit** quand un paramètre spécifie une valeur pour `vmSize` :

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

Ensuite, `hardwareProfile` utilise une expression pour que `vmSize` référence la valeur du paramètre :

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>Les valeurs Min et Max sont des nombres

Nom du test : **Les valeurs Min et Max sont des nombres**

Quand vous définissez un paramètre avec `minValue` et `maxValue`, spécifiez ces valeurs comme nombres. Vous devez utiliser `minValue` et `maxValue` comme paire, sans quoi le test échoue.

Dans l’exemple suivant, le test **échoue**, car `minValue` et `maxValue` sont des chaînes.

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

Dans l’exemple suivant, le test **échoue**, car seul `minValue` est utilisé.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

Dans l’exemple suivant, le test **réussit**, car `minValue` et `maxValue` sont des nombres.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>Paramètre des artefacts défini correctement

Nom du test : **paramètre artifacts**

Lorsque vous incluez des paramètres pour `_artifactsLocation` et `_artifactsLocationSasToken`, utilisez les valeurs par défaut et les types appropriés. Les conditions suivantes doivent être satisfaites pour réussir ce test :

- Si vous fournissez un paramètre, vous devez fournir l’autre.
- `_artifactsLocation` doit être de type `string`.
- `_artifactsLocation` doit posséder une valeur par défaut dans le modèle principal.
- `_artifactsLocation` ne peut pas posséder une valeur par défaut dans un modèle imbriqué.
- Par défaut, `_artifactsLocation` doit être définie sur `"[deployment().properties.templateLink.uri]"` ou sur l’URL de référentiel brute.
- `_artifactsLocationSasToken` doit être de type `secureString`.
- `_artifactsLocationSasToken` ne peut avoir qu’une chaîne vide comme valeur par défaut.
- `_artifactsLocationSasToken` ne peut pas posséder une valeur par défaut dans un modèle imbriqué.

## <a name="declared-variables-must-be-used"></a>Les variables déclarées doivent être utilisées

Nom du test : **Les variables doivent être référencées**

Ce test recherche des variables qui ne sont pas utilisées dans le modèle ou dans une expression valide. Pour améliorer la lisibilité de votre modèle, supprimez toutes les variables qui sont définies mais qui ne sont pas utilisées.

Les variables qui utilisent l’élément `copy` pour itérer des valeurs doivent être référencées. Pour plus d’informations, consultez [Itération de variable dans les modèles ARM](copy-variables.md).

Dans l’exemple suivant, le test **échoue**, car la variable qui utilise l’élément `copy` n’est pas référencée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

Dans l’exemple suivant, le test **échoue**, car l’expression qui référence une variable ne contient pas le crochet ouvrant (`[`).

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

Dans l’exemple suivant, le test **réussit**, car la variable est référencée dans `outputs`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**, car l’expression est valide.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>La variable dynamique ne doit pas utiliser concat

Nom du test : **Les références de variables dynamiques ne doivent pas utiliser concat**

Parfois, vous devez construire dynamiquement une variable basée sur la valeur d’une autre variable ou d’un autre paramètre. N’utilisez pas la fonction [concat](template-functions-string.md#concat) lors de la définition de la valeur. Au lieu de cela, utilisez un objet qui comprend les options disponibles et récupérez dynamiquement l’une des propriétés de l’objet au cours du déploiement.

Dans l’exemple suivant, le test **réussit**. La variable `currentImage` est définie de manière dynamique au cours du déploiement.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>Utiliser la version récente de l’API

Nom du test : **apiVersions doit être récent**

La version d’API de chaque ressource doit correspondre à une version récente, codée en dur comme chaîne. Le test évalue la version d’API dans votre modèle par rapport aux versions du fournisseur de ressources dans le cache du kit de ressources. Une version d’API datant de moins de deux ans par rapport à la date d’exécution du test est considérée comme récente. N’utilisez pas de préversion quand une version plus récente est disponible.

Un avertissement signalant qu’une version d’API n’a pas été trouvée indique uniquement que la version n’est pas incluse dans le cache du kit de ressources. L’utilisation de la version la plus récente d’une API (ce qui est recommandé) peut générer l’avertissement.

Apprenez-en davantage sur le [cache du kit de ressources](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

Dans l’exemple suivant, le test **échoue**, car la version d’API date de plus de deux ans.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

Dans l’exemple suivant, le test **échoue**, car une préversion est utilisée alors qu’une version plus récente est disponible.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

Dans l’exemple suivant, le test **réussit**, car il s’agit d’une version récente qui n’est pas une préversion.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>Utilisez une version d’API codée en dur

Nom du test : **Providers apiVersions n’est pas autorisé**

La version d’API d’un type de ressource détermine les propriétés disponibles. Fournissez une version d’API codée en dur dans votre modèle. Ne récupérez pas une version d’API déterminée durant le déploiement, car vous ne saurez pas quelles propriétés sont disponibles.

Dans l’exemple suivant, le test **échoue**.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

Dans l’exemple suivant, le test **réussit**.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>Les propriétés ne peuvent pas être vides

Nom du test : **Le modèle ne doit pas contenir d’espaces vides**

Ne codez pas en dur les propriétés avec une valeur vide. Les valeurs vides incluent des chaînes, des objets ou des tableaux null et vides. Si une propriété est définie sur une valeur vide, supprimez-la de votre modèle. Vous pouvez définir une propriété sur une valeur vide pendant le déploiement, par exemple par le biais d’un paramètre.

La propriété `template` dans un [modèle imbriqué](linked-templates.md#nested-template) peut inclure des propriétés vides. Pour plus d’informations sur les modèles imbriqués, consultez [déploiements Microsoft.Resources](/azure/templates/microsoft.resources/deployments).

Dans l’exemple suivant, le test **échoue**, car des propriétés sont vides.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

Dans l’exemple suivant, le test **réussit**, car les propriétés incluent des valeurs.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>Utiliser les fonctions ID de ressource

Nom du test : **Les ID doivent être dérivés de ResourceID**

Lorsque vous spécifiez un ID de ressource, utilisez l’une des fonctions d’ID de ressource. Les fonctions autorisées sont les suivantes :

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

N’utilisez pas la fonction concat pour créer un ID de ressource. Dans l’exemple suivant, le test **échoue**.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>La fonction ResourceId possède des paramètres corrects

Nom du test : **ResourceIds ne doit pas contenir**

Lors de la génération d’ID de ressource, n’utilisez pas de fonctions inutiles pour les paramètres facultatifs. Par défaut, la fonction [resourceId](template-functions-resource.md#resourceid) utilise le groupe actuel d’abonnements et de ressources. Vous n’avez pas besoin de fournir ces valeurs.

Dans l’exemple suivant, le test **échoue**, car vous n’avez pas besoin de fournir l’ID d’abonnement et le nom de groupe de ressources actuels.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Ce test s’applique à :

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [reference](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

Concernant `reference` et `list*`, le test **échoue** quand vous utilisez `concat` pour construire l’ID de ressource.

## <a name="dependson-best-practices"></a>Bonnes pratiques pour dependsOn

Nom du test : **Bonnes pratiques pour dependsOn**

Lors de la définition des dépendances de déploiement, n’utilisez pas la fonction [if](template-functions-logical.md#if) pour tester une condition. Si une ressource dépend d’une ressource qui est [déployée de manière conditionnelle](conditional-resource-deployment.md), définissez la dépendance comme vous le feriez avec n’importe quelle ressource. Quand une ressource conditionnelle n’est pas déployée, Azure Resource Manager la supprime automatiquement des dépendances nécessaires.

L’élément `dependsOn` ne peut pas commencer par une fonction [concat](template-functions-array.md#concat).

Dans l’exemple suivant, le test **échoue**, car il contient une fonction `if`.

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Dans l’exemple suivant, le test **échoue**, car il commence par `concat`.

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

Dans l’exemple suivant, le test **réussit**.

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Les déploiements imbriqués ou liés ne peuvent pas utiliser debug

Nom du test : **Les ressources de déploiement ne doivent pas être en mode débogage**

Quand vous définissez un [modèle imbriqué ou lié](linked-templates.md) avec le type de ressource `Microsoft.Resources/deployments`, vous pouvez activer le [débogage](/azure/templates/microsoft.resources/deployments#debugsetting-object). Le débogage est utilisé quand vous devez tester un modèle, mais que vous pouvez exposer des informations sensibles. Avant d’utiliser le modèle en production, désactivez le débogage. Vous pouvez supprimer l’objet `debugSetting` ou définir la propriété `detailLevel` sur `none`.

Dans l’exemple suivant, le test **échoue**.

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>Les noms d’utilisateur admin ne peuvent pas être des valeurs littérales

Nom du test : **adminUsername ne doit pas être un littéral**

Quand vous définissez un `adminUserName`, n’utilisez pas de valeur littérale. Créez un paramètre pour le nom d’utilisateur et utilisez une expression pour référencer la valeur du paramètre.

Dans l’exemple suivant, le test **échoue** avec une valeur littérale.

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

Dans l’exemple suivant, le test **réussit** avec une expression.

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Utiliser la dernière image de machine virtuelle

Nom du test : **Les images de machine virtuelle doivent utiliser la dernière version**

Ce test est désactivé, mais la sortie indique qu’il a réussi. Une bonne pratique consiste à vérifier les critères suivants dans votre modèle :

Si votre modèle contient une machine virtuelle avec une image, assurez-vous qu’elle utilise la version la plus récente de l’image.

## <a name="use-stable-vm-images"></a>Utiliser des images de machine virtuelle stables

Nom du test : **Les machines virtuelles ne doivent pas être une préversion**

Les machines virtuelles ne doivent pas utiliser d’images de préversion. Le test contrôle `storageProfile` pour vérifier que `imageReference` n’utilise pas une chaîne qui contient _preview_. et que _preview_ n’est pas utilisé dans les propriétés `offer`, `sku` ou `version` de `imageReference`.

Pour plus d’informations sur la propriété `imageReference`, consultez [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object) et [Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object).

Dans l’exemple suivant, le test **échoue**, car `imageReference` est une chaîne contenant _preview_.

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

Dans l’exemple suivant, le test **échoue** quand _preview_ est utilisé dans `offer`, `sku` ou `version`.

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>Ne pas utiliser l’extension ManagedIdentity

Nom du test : **ManagedIdentityExtension ne doit pas être utilisée**

N’appliquez pas l’extension `ManagedIdentity` à une machine virtuelle. Déconseillée depuis 2019, l'extension ne doit plus être utilisée.

## <a name="outputs-cant-include-secrets"></a>Les sorties ne peuvent pas comprendre de secrets

Nom du test : **Les sorties ne doivent pas contenir de secrets**

N’incluez pas de valeurs dans la section `outputs`, qui expose potentiellement des secrets. Par exemple, des paramètres sécurisés de type `secureString` ou `secureObject` ou des fonctions [list*](template-functions-resource.md#list) comme `listKeys`.

La sortie d’un modèle est stockée dans l’historique de déploiement ; aussi un utilisateur malveillant peut-il y trouver ces informations.

Dans l’exemple suivant, le test **échoue**, car il comprend un paramètre sécurisé dans une valeur de sortie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

L’exemple suivant **échoue**, car il utilise une fonction [list*](template-functions-resource.md#list) dans les sorties.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Utiliser protectedSettings pour les secrets commandToExecute

Nom du test : **CommandToExecute doit utiliser ProtectedSettings pour les secrets**

Pour les ressources de type `CustomScript`, utilisez la propriété `protectedSettings` chiffrée quand `commandToExecute` inclut des données secrètes comme un mot de passe. Par exemple, les données secrètes peuvent être utilisées dans des paramètres sécurisés de type `secureString` ou `secureObject`, des fonctions [list*](template-functions-resource.md#list) comme `listKeys` ou des scripts personnalisés.

N’utilisez pas de données secrètes dans l’objet `settings`, car il utilise du texte clair. Pour plus d’informations, consultez [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions), [Windows](
/azure/virtual-machines/extensions/custom-script-windows) ou [Linux](../../virtual-machines/extensions/custom-script-linux.md).

Dans l’exemple suivant, le test **échoue**, car `settings` utilise `commandToExecute` avec un paramètre sécurisé.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

Dans l’exemple suivant, le test **échoue**, car `settings` utilise `commandToExecute` avec une fonction `listKeys`.

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

Dans l’exemple suivant, le test **réussit**, car `protectedSettings` utilise `commandToExecute` avec un paramètre sécurisé.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

Dans l’exemple suivant, le test **réussit**, car `protectedSettings` utilise `commandToExecute` avec une fonction `listKeys`.

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>Utilisez des versions d’API récentes dans les fonctions reference

Nom du test : **apiVersions Should Be Recent In Reference Functions** (Les versions d’API doivent être récentes dans les fonctions reference)

La version d’API utilisée dans une fonction [reference](template-functions-resource.md#reference) doit être récente et ne doit pas être une préversion. Le test évalue la version d’API dans votre modèle par rapport aux versions du fournisseur de ressources dans le cache du kit de ressources. Une version d’API datant de moins de deux ans par rapport à la date d’exécution du test est considérée comme récente.

Un avertissement signalant qu’une version d’API n’a pas été trouvée indique uniquement que la version n’est pas incluse dans le cache du kit de ressources. L’utilisation de la version la plus récente d’une API (ce qui est recommandé) peut générer l’avertissement.

Apprenez-en davantage sur le [cache du kit de ressources](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache).

Dans l’exemple suivant, le test **échoue**, car la version d’API date de plus de deux ans.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

Dans l’exemple suivant, le test **échoue**, car la version d’API est une préversion.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

Dans l’exemple suivant, le test **réussit**, car la version d’API date de moins de deux ans et n’est pas une préversion.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>Utilisez un type et un nom dans les fonctions resourceId

Nom du test : **Resources Should Not Be Ambiguous** (Les ressources ne doivent pas être ambiguës)

Ce test est désactivé, mais la sortie indique qu’il a réussi. Une bonne pratique consiste à vérifier les critères suivants dans votre modèle :

Une fonction [resourceId](template-functions-resource.md#resourceid) doit inclure un type de ressource et un nom de ressource. Ce test recherche toutes les fonctions `resourceId` du modèle et vérifie que la ressource est utilisée dans le modèle avec la syntaxe correcte. Dans le cas contraire, la fonction est considérée comme ambiguë.

Par exemple, une fonction `resourceId` est considérée comme ambiguë :

- Quand une ressource est introuvable dans le modèle et qu’aucun groupe de ressources n’est spécifié.
- Si une ressource contient une condition et qu’aucun groupe de ressources n’est spécifié.
- Si une ressource associée contient certains segments de nom, mais pas tous. Par exemple, une ressource enfant contient plusieurs segments de noms. Pour plus d’informations, consultez les [notes sur resourceId](template-functions-resource.md#remarks-3).

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>Utilisez l’étendue inner pour les paramètres sécurisés de déploiements imbriqués

Nom du test :**Secure Params In Nested Deployments** (Paramètres sécurisés dans des déploiements imbriqués)

Utilisez l’objet `expressionEvaluationOptions` du modèle imbriqué avec l’étendue `inner` pour évaluer les expressions qui contiennent des paramètres sécurisés de type `secureString` ou `secureObject` ou des fonctions [list*](template-functions-resource.md#list) comme `listKeys`. Si l’étendue `outer` est utilisée, les expressions sont évaluées en texte clair dans l’étendue du modèle parent. La valeur sécurisée est ensuite visible par toute personne ayant accès à l’historique de déploiement. La valeur par défaut de `expressionEvaluationOptions` est `outer`.

Pour plus d’informations sur les modèles imbriqués, consultez [Déploiements Microsoft.Resources](/azure/templates/microsoft.resources/deployments) et [Étendue de l’évaluation d’expressions dans les modèles imbriqués](linked-templates.md#expression-evaluation-scope-in-nested-templates).

L’exemple suivant **échoue**, car `expressionEvaluationOptions` utilise l’étendue `outer` pour évaluer des paramètres sécurisés ou des fonctions `list*`.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

L’exemple suivant **réussit**, car `expressionEvaluationOptions` utilise l’étendue `inner` pour évaluer des paramètres sécurisés ou des fonctions `list*`.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’exécution de la boîte à outils de test, consultez [Utiliser le kit de ressources de test de modèle ARM](test-toolkit.md).
- Pour suivre un module Microsoft Learn couvrant l’utilisation du kit de ressources de test, consultez [Prévisualisation des modifications et validation des ressources Azure à l’aide de simulations et du kit de ressources de test de modèle ARM](/learn/modules/arm-template-test/).
- Pour tester des fichiers de paramètres, consultez [Cas de test pour les fichiers de paramètres](parameters.md).
- Pour les tests createUiDefinition, consultez [Cas de test pour createUiDefinition.json](createUiDefinition-test-cases.md).
- Pour obtenir des informations concernant les tests pour tous les fichiers, consultez [Cas de test pour tous les fichiers](all-files-test-cases.md).
