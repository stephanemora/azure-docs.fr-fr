---
title: Paramètres dans les modèles
description: Explique comment définir des paramètres dans un modèle Azure Resource Manager (ARM).
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 4fa624763b3a0eaa0277882fcb8493e96e24e7a5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960204"
---
# <a name="parameters-in-arm-templates"></a>Paramètres dans les modèles ARM

Cet article explique comment définir et utiliser des paramètres dans un modèle Azure Resource Manager (ARM). En fournissant des valeurs différentes pour les paramètres, vous pouvez réutiliser un modèle pour différents environnements.

Resource Manager résout les valeurs des paramètres avant de démarrer les opérations de déploiement. Chaque fois que le paramètre est utilisé dans le modèle, Resource Manager le remplace par la valeur résolue.

Chaque paramètre doit être défini sur l’un des [types de données](data-types.md).

## <a name="minimal-declaration"></a>Déclaration minimale

Au minimum, chaque paramètre a besoin d’un nom et d’un type.

Lorsque vous déployez un modèle via le portail Azure, les noms de paramètres en casse mixte sont convertis en noms séparés par des espaces. Par exemple, *demoString* dans l’exemple suivant est représenté sous la forme *Demo String*. Pour plus d’informations, consultez [Utiliser un bouton de déploiement pour déployer des modèles à partir du référentiel GitHub](./deploy-to-azure-button.md) et [Déployer des ressources avec des modèles ARM et le portail Azure](./deploy-portal.md).

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

## <a name="secure-parameters"></a>Paramètres sécurisés

Vous pouvez marquer les paramètres de chaîne ou d’objet comme sécurisés. La valeur d’un paramètre sécurisé n’est pas enregistrée dans l’historique de déploiement et n’est pas journalisée.

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

## <a name="allowed-values"></a>Valeurs autorisées

Vous pouvez définir des valeurs autorisées pour un paramètre. Vous fournissez les valeurs autorisées dans un tableau. Le déploiement échoue pendant la validation si une valeur transmise pour le paramètre n’est pas l’une des valeurs autorisées.

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

## <a name="default-value"></a>Valeur par défaut

Vous pouvez spécifier une valeur par défaut pour un paramètre. La valeur par défaut est utilisée quand aucune valeur n’est fournie pendant le déploiement.

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Pour spécifier une valeur par défaut ainsi que d’autres propriétés pour le paramètre, utilisez la syntaxe suivante.

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

Vous pouvez utiliser des expressions avec la valeur par défaut. Vous ne pouvez pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list) dans la section parameters. Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des paramètres sont résolus.

Les expressions ne sont pas autorisées avec d’autres propriétés de paramètre.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

Vous pouvez utiliser une autre valeur de paramètre pour générer une valeur par défaut. Le modèle suivant construit un nom de plan d’hôte à partir du nom de site.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

## <a name="length-constraints"></a>Contraintes de longueur

Vous pouvez spécifier des longueurs minimale et maximale pour les paramètres de chaîne et de tableau. Vous pouvez définir une contrainte ou les deux. Pour les chaînes, la longueur indique le nombre de caractères. Pour les tableaux, la longueur indique le nombre d’éléments dans le tableau.

L’exemple suivant déclare deux paramètres. Un paramètre est destiné à un nom de compte de stockage qui doit compter entre 3 et 24 caractères. L’autre paramètre est un tableau qui doit compter entre 1 et 5.

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

## <a name="integer-constraints"></a>Contraintes d’entier

Vous pouvez définir des valeurs minimales et maximales pour les paramètres de type entier. Vous pouvez définir une contrainte ou les deux.

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

## <a name="description"></a>Description

Vous pouvez ajouter une description à un paramètre pour aider les utilisateurs de votre modèle à comprendre la valeur à fournir. Lorsque vous déployez le modèle par le biais du portail, le texte que vous fournissez dans la description est automatiquement utilisé comme une info-bulle pour ce paramètre. Ajoutez une description uniquement quand le texte fournit des informations en plus de celles qui peuvent être déduites du nom du paramètre.

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

## <a name="use-parameter"></a>Utiliser un paramètre

Pour référencer la valeur d’un paramètre, utilisez la fonction [parameters](template-functions-deployment.md#parameters). L’exemple suivant utilise une valeur de paramètre pour un nom de coffre de clés.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

## <a name="objects-as-parameters"></a>Objets en tant que paramètres

Vous pouvez organiser des valeurs connexes en les transmettant en tant qu’objet. Cette approche réduit également le nombre de paramètres dans le modèle.

L’exemple suivant illustre un paramètre qui est un objet. La valeur par défaut affiche les propriétés attendues pour l’objet. Ces propriétés sont utilisées lors de la définition de la ressource à déployer.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
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
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants illustrent des scénarios d’utilisation de paramètres.

|Modèle  |Description  |
|---------|---------|
|[Paramètres avec fonctions pour les valeurs par défaut](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Montre comment utiliser les fonctions de modèle durant la définition des valeurs par défaut des paramètres. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |
|[Objet de paramètre](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Montre comment utiliser un objet pour un paramètre. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés disponibles pour les paramètres, consultez [Présentation de la structure et de la syntaxe des modèles ARM](./syntax.md).
* Pour en savoir plus sur le passage de valeurs de paramètre sous forme de fichier, consultez [Créer un fichier de paramètres Resource Manager](parameter-files.md).
* Pour obtenir des recommandations sur la création de paramètres, consultez [Bonnes pratiques - Paramètres](./best-practices.md#parameters).