---
title: Paramètres dans les modèles
description: Explique comment définir des paramètres dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 3c0c40d5ddb47144fb42c99a9377d1b6e66854b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476191"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Paramètres dans les modèles Azure Resource Manager

Cet article explique comment définir et utiliser des paramètres dans votre modèle Azure Resource Manager. En fournissant des valeurs différentes pour les paramètres, vous pouvez réutiliser un modèle pour différents environnements.

Resource Manager résout les valeurs des paramètres avant de démarrer les opérations de déploiement. Chaque fois que le paramètre est utilisé dans le modèle, Resource Manager le remplace par la valeur résolue.

## <a name="define-parameter"></a>Définir un paramètre

L’exemple suivant illustre une définition de paramètre simple. Il définit un paramètre nommé **storageSKU**. Le paramètre est une valeur de chaîne et accepte uniquement des valeurs qui sont valides pour l’utilisation pour laquelle il est prévu. Il utilise une valeur par défaut quand aucune valeur n’est fournie au cours du déploiement.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Utiliser un paramètre

Dans le modèle, vous référencez la valeur du paramètre à l’aide de la fonction [parameters](template-functions-deployment.md#parameters). Dans l’exemple suivant, la valeur du paramètre est utilisée afin de définir la référence (SKU) pour le compte de stockage.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Fonctions des modèles de gestionnaire des ressources Azure

Quand vous spécifiez la valeur par défaut d’un paramètre, vous pouvez utiliser la plupart des fonctions de modèle. Vous pouvez utiliser une autre valeur de paramètre pour générer une valeur par défaut. Le modèle suivant illustre l’utilisation de fonctions avec la valeur par défaut. Si aucun nom n’est fourni pour le site, il crée une valeur de chaîne unique et l’ajoute au **site**. Quand aucun nom n’est fourni pour le plan de l’hôte, il prend la valeur du site et ajoute **-plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Vous ne pouvez pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list) dans la section parameters. Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des paramètres sont résolus.

## <a name="objects-as-parameters"></a>Objets en tant que paramètres

Il peut s’avérer plus facile d’organiser des valeurs connexes en les transmettant en tant qu’objets. Cette approche réduit également le nombre de paramètres dans le modèle.

L’exemple suivant illustre un paramètre qui est un objet. La valeur par défaut affiche les propriétés attendues pour l’objet.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Vous référencez les propriétés de l’objet en utilisant l’opérateur point.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants illustrent des scénarios d’utilisation de paramètres.

|Modèle  |Description  |
|---------|---------|
|[Paramètres avec fonctions pour les valeurs par défaut](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Montre comment utiliser les fonctions de modèle durant la définition des valeurs par défaut des paramètres. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |
|[Objet de paramètre](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Montre comment utiliser un objet pour un paramètre. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les propriétés disponibles pour les paramètres, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
* Pour en savoir plus sur le passage de valeurs de paramètre sous forme de fichier, consultez [Créer un fichier de paramètres Resource Manager](parameter-files.md).
* Pour obtenir des recommandations sur la création de paramètres, consultez [Bonnes pratiques - Paramètres](template-best-practices.md#parameters).
