---
title: Variables dans les modèles
description: Explique comment définir des variables dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75476163"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variables dans un modèle Azure Resource Manager

Cet article explique comment définir et utiliser des variables dans votre modèle Azure Resource Manager. Vous utilisez des variables pour simplifier votre modèle. Au lieu de répéter des expressions complexes tout au long de votre modèle, vous définissez une variable qui contient l’expression complexe. Ensuite, vous référencez cette variable en fonction des besoins dans votre modèle.

Resource Manager résout les variables avant de démarrer les opérations de déploiement. Chaque fois que la variable est utilisée dans le modèle, Resource Manager la remplace par la valeur résolue.

## <a name="define-variable"></a>Définir une variable

L’exemple suivant montre une définition de variable. Il crée une valeur de chaîne pour le nom d’un compte de stockage. Il utilise plusieurs fonctions de modèle pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Vous ne pouvez pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list) dans la section variables. Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des variables sont résolues.

## <a name="use-variable"></a>Utiliser une variable

Dans le modèle, vous référencez la valeur du paramètre à l’aide de la fonction [variables](template-functions-deployment.md#variables). L’exemple suivant montre comment utiliser la variable pour une propriété de ressource.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez définir des variables qui contiennent des valeurs associées pour la configuration d’un environnement. Vous définissez la variable en tant qu’objet avec les valeurs. L’exemple suivant illustre un objet qui contient des valeurs pour deux environnements : **test** et **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Dans Paramètres, créez une valeur qui indique les valeurs de configuration à utiliser.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Pour récupérer les paramètres de l’environnement spécifié, utilisez la variable et le paramètre ensemble.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants illustrent des scénarios d’utilisation de variables.

|Modèle  |Description  |
|---------|---------|
| [définitions de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Montre les différents types de variables. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [variable de configuration](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustre l’utilisation d’une variable qui définit des valeurs de configuration. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [règles de sécurité réseau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) et [fichier de paramètres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crée un tableau au bon format pour attribuer des règles de sécurité à un groupe de sécurité réseau. |

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les propriétés disponibles pour les variables, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
* Pour obtenir des recommandations sur la création de variables, consultez [Bonnes pratiques - Variables](template-best-practices.md#variables).
