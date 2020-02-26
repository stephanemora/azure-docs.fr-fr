---
title: Modèle de syntaxe et d’expressions
description: Décrit la syntaxe JSON déclarative pour les modèles Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207398"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe et expressions dans les modèles Azure Resource Manager

La syntaxe de base du modèle est JSON. Toutefois, vous pouvez utiliser des expressions et fonctions pour étendre les valeurs JSON disponibles dans le modèle.  Les expressions commencent et se terminent avec des crochets : `[` et `]`, respectivement. La valeur de l’expression est évaluée lorsque le modèle est déployé. Une expression peut retourner une chaîne, un entier, un booléen, un tableau ou un objet.

Une expression de modèle ne peut pas dépasser 24 576 caractères.

## <a name="use-functions"></a>Utiliser les fonctions

Azure Resource Manager fournit des [fonctions](template-functions.md) que vous pouvez utiliser dans un modèle. L’exemple suivant montre une expression qui utilise une fonction dans la valeur par défaut d’un paramètre :

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dans l’expression, la syntaxe `resourceGroup()` appelle une des fonctions fournies par Resource Manager pour une utilisation dans un modèle. Dans ce cas, il s’agit de la fonction [resourceGroup](template-functions-resource.md#resourcegroup). Comme en JavaScript, les appels de fonction sont formatés comme suit : `functionName(arg1,arg2,arg3)`. La syntaxe `.location` récupère une propriété de l’objet retourné par cette fonction.

Les fonctions des modèles et leurs paramètres ne respectent pas la casse. Par exemple, Resource Manager résout **variables('var1')** et **VARIABLES('VAR1')** de la même manière. Lors de l’évaluation, la fonction préserve la casse sauf si elle la modifie expressément (toUpper ou toLower, par exemple). Certains types de ressources peuvent avoir des exigences de casse indépendantes de la façon dont les fonctions sont évaluées.

Pour passer une valeur de chaîne en tant que paramètre à une fonction, utilisez des guillemets simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

La plupart des fonctions opèrent de la même façon si elles sont déployées sur un groupe de ressources, un abonnement, un groupe d’administration ou un locataire. Les fonctions suivantes présentent des restrictions en fonction de l’étendue :

* [resourceGroup](template-functions-resource.md#resourcegroup) - peut être utilisée uniquement dans les déploiements sur un groupe de ressources.
* [resourceId](template-functions-resource.md#resourceid) - peut être utilisée dans n’importe quelle étendue, mais les paramètres valides changent en fonction de l’étendue.
* [subscription](template-functions-resource.md#subscription) - peut être utilisée uniquement dans les déploiements sur un groupe de ressources ou un abonnement.

## <a name="escape-characters"></a>Caractères d'échappement

Pour avoir une chaîne littérale qui commence par un crochet gauche `[` et se termine par un crochet droit `]`, sans qu’elle soit interprétée comme une expression, ajoutez un crochet supplémentaire. La chaîne commence alors par `[[`. Par exemple, la variable :

```json
"demoVar1": "[[test value]"
```

Est résolu en `[test value]`.

Toutefois, si la chaîne littérale ne se termine par un crochet, n’utilisez pas le caractère d’échappement pour le premier crochet. Par exemple, la variable :

```json
"demoVar2": "[test] value"
```

Est résolu en `[test] value`.

Pour échapper les guillemets doubles dans une expression, comme l’ajout d’un objet JSON dans le modèle, utilisez la barre oblique inverse.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Valeurs Null

Pour définir une propriété sur la valeur Null, vous pouvez utiliser **null** ou **[json('null')]** . La [fonction json](template-functions-array.md#json) retourne un objet vide quand vous fournissez `null` comme paramètre. Dans les deux cas, les modèles Resource Manager traitent les expressions comme si la propriété en était absente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](template-functions.md).
* Pour plus d’informations sur les fichiers de modèle, voir [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
