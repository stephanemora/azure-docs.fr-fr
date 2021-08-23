---
title: Variables dans les modèles
description: Explique comment définir des variables dans un modèle Azure Resource Manager (ARM).
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 0a9b49c36f25295ab06e724773e1494c10eb1f08
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893111"
---
# <a name="variables-in-arm-templates"></a>Variables dans les modèles ARM

Cet article explique comment définir et utiliser des variables dans un modèle Azure Resource Manager (ARM). Vous utilisez des variables pour simplifier votre modèle. Au lieu de répéter des expressions complexes tout au long de votre modèle, vous définissez une variable qui contient l’expression complexe. Ensuite, vous utilisez cette variable en fonction des besoins dans votre modèle.

Resource Manager résout les variables avant de démarrer les opérations de déploiement. Chaque fois que la variable est utilisée dans le modèle, Resource Manager la remplace par la valeur résolue.

## <a name="define-variable"></a>Définir une variable

Lorsque vous définissez une variable, vous ne spécifiez pas de [type de données](data-types.md) associé. Vous indiquez plutôt une valeur ou une expression de modèle. Le type de variable est déduit de la valeur résolue. Dans l’exemple suivant, la valeur d’une variable est définie sur une chaîne.

```json
"variables": {
  "stringVar": "example value"
},
```

Pour construire la variable, vous pouvez utiliser la valeur d’un paramètre ou d’une autre variable.

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

Vous pouvez utiliser des [fonctions de modèle](template-functions.md) pour construire la valeur de la variable.

Dans l’exemple suivant, une valeur de chaîne est créée pour un nom de compte de stockage. Il utilise plusieurs fonctions de modèle pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Vous ne pouvez pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list) dans la déclaration de variable. Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des variables sont résolues.

## <a name="use-variable"></a>Utiliser une variable

L’exemple suivant montre comment utiliser la variable pour une propriété de ressource.

Pour référencer la valeur de la variable, utilisez la fonction [variables](template-functions-deployment.md#variables).

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Exemple de modèle

Le modèle suivant ne déploie aucune ressource. Il montre quelques façons de déclarer des variables de types différents.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez définir des variables qui contiennent des valeurs associées pour la configuration d’un environnement. Vous définissez la variable en tant qu’objet avec les valeurs. L’exemple suivant illustre un objet qui contient des valeurs pour deux environnements : **test** et **prod**. Transmettez l’une de ces valeurs au cours du déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés disponibles pour les variables, consultez [Présentation de la structure et de la syntaxe des modèles ARM](./syntax.md).
* Pour obtenir des recommandations sur la création de variables, consultez [Bonnes pratiques - Variables](./best-practices.md#variables).
* Pour obtenir un exemple de modèle qui attribue des règles de sécurité à un groupe de sécurité réseau, consultez le code pour les [règles de sécurité réseau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) et le [fichier de paramètres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).