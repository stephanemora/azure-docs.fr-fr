---
title: Variables dans les modèles
description: Explique comment définir des variables dans un modèle Azure Resource Manager (ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874432"
---
# <a name="variables-in-arm-template"></a>Variables dans un modèle ARM

Cet article explique comment définir et utiliser des variables dans un modèle Azure Resource Manager (ARM). Vous utilisez des variables pour simplifier votre modèle. Au lieu de répéter des expressions complexes tout au long de votre modèle, vous définissez une variable qui contient l’expression complexe. Ensuite, vous référencez cette variable en fonction des besoins dans votre modèle.

Resource Manager résout les variables avant de démarrer les opérations de déploiement. Chaque fois que la variable est utilisée dans le modèle, Resource Manager la remplace par la valeur résolue.

## <a name="define-variable"></a>Définir une variable

Lorsque vous définissez une variable, fournissez une valeur ou une expression de modèle qui se résout en un [type de données](template-syntax.md#data-types). Vous pouvez utiliser la valeur d’un paramètre ou d’une autre variable lors de la construction de la variable.

Vous pouvez utiliser des [fonctions de modèle](template-functions.md) dans la déclaration de variable, mais vous ne pouvez pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list). Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des variables sont résolues.

L’exemple suivant montre une définition de variable. Il crée une valeur de chaîne pour le nom d’un compte de stockage. Il utilise plusieurs fonctions de modèle pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

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

## <a name="example-template"></a>Exemple de modèle

Le modèle suivant ne déploie aucune ressource. Il montre simplement quelques façons de déclarer des variables.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez définir des variables qui contiennent des valeurs associées pour la configuration d’un environnement. Vous définissez la variable en tant qu’objet avec les valeurs. L’exemple suivant illustre un objet qui contient des valeurs pour deux environnements : **test** et **prod**. Vous transmettez l’une de ces valeurs au cours du déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés disponibles pour les variables, consultez [Présentation de la structure et de la syntaxe des modèles ARM](template-syntax.md).
* Pour obtenir des recommandations sur la création de variables, consultez [Bonnes pratiques - Variables](template-best-practices.md#variables).
* Pour obtenir un exemple de modèle qui attribue des règles de sécurité à un groupe de sécurité réseau, consultez le code pour les [règles de sécurité réseau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) et le [fichier de paramètres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
