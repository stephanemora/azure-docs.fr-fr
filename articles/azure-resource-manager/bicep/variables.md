---
title: Variables dans Bicep
description: Décrit comment définir des variables dans Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: b2f696adbad88cd424f2292b333069a7b80a13b2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634954"
---
# <a name="variables-in-bicep"></a>Variables dans Bicep

Cet article décrit comment définir et utiliser des variables dans votre fichier Bicep. Vous utilisez des variables pour simplifier le développement de vos fichiers Bicep. Au lieu de répéter des expressions complexes tout au long de votre fichier Bicep, vous définissez une variable qui contient l’expression complexe. Ensuite, vous utilisez cette variable en fonction des besoins dans votre fichier Bicep.

Resource Manager résout les variables avant de démarrer les opérations de déploiement. Chaque fois que la variable est utilisée dans le fichier Bicep, Resource Manager la remplace par la valeur résolue.

## <a name="define-variable"></a>Définir une variable

Lorsque vous définissez une variable, vous ne spécifiez pas de [type de données](data-types.md) associé. Vous indiquez plutôt une valeur ou une expression de modèle. Le type de variable est déduit de la valeur résolue. Dans l’exemple suivant, la valeur d’une variable est définie sur une chaîne.

```bicep
var stringVar = 'example value'
```

Vous pouvez utiliser la valeur d’un paramètre ou d’une autre variable lors de la construction de la variable.

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

Vous pouvez utiliser les [fonctions Bicep](bicep-functions.md) pour construire la valeur de la variable. Les fonctions [reference](bicep-functions-resource.md#reference) et [list](bicep-functions-resource.md#list) sont valides lors de la déclaration d’une variable.

Dans l’exemple suivant, une valeur de chaîne est créée pour un nom de compte de stockage. Il utilise plusieurs fonctions de Bicep pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

## <a name="use-variable"></a>Utiliser une variable

L’exemple suivant montre comment utiliser la variable pour une propriété de ressource. Vous faites référence à la valeur de la variable en indiquant le nom de la variable : `storageName`.

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

Étant donné que les noms de compte de stockage doivent utiliser des minuscules, la variable `storageName` utilise la fonction `toLower` pour mettre la valeur `storageNamePrefix` en minuscules. La fonction `uniqueString` crée une valeur unique à partir de l’ID du groupe de ressources. Les valeurs sont concaténées en une chaîne.

## <a name="example-template"></a>Exemple de modèle

Le modèle suivant ne déploie aucune ressource. Il montre quelques façons de déclarer des variables de types différents.

:::code language="bicep" source="~/azure-docs-bicep-samples/bicep/variables.bicep":::

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez définir des variables qui contiennent des valeurs associées pour la configuration d’un environnement. Vous définissez la variable en tant qu’objet avec les valeurs. L’exemple suivant illustre un objet qui contient des valeurs pour deux environnements : **test** et **prod**. Transmettez l’une de ces valeurs au cours du déploiement.

:::code language="bicep" source="~/azure-docs-bicep-samples/bicep/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les propriétés disponibles pour les variables, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
