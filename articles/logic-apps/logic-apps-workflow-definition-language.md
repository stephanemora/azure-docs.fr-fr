---
title: Référence de schéma du langage de définition de workflow - Azure Logic Apps | Microsoft Docs
description: Écrivez des définitions de flux de travail personnalisées pour Azure Logic Apps avec le langage de définition de flux de travail
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3c17ec2133e278b17475e4988e1e9766b1349ba4
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734638"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Référence de schéma du langage de définition de workflow dans Azure Logic Apps

Quand vous créez un flux de travail d’application logique avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), la définition sous-jacente de votre flux de travail décrit la logique réelle qui s’exécute pour votre application logique. Cette description suit une structure définie et validée par le schéma de langage de définition de workflow, qui utilise [JSON (JavaScript Objet Notation)](https://www.json.org/).

## <a name="workflow-definition-structure"></a>Structure d’une définition de flux de travail

Une définition de flux de travail présente au moins un déclencheur qui instancie votre application logique, ainsi qu’une ou plusieurs actions exécutées par votre application logique.

Voici la structure de haut niveau d’une définition de flux de travail :

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Élément | Obligatoire | Description |
|---------|----------|-------------|
| Définition | Oui | Élément de départ de votre définition de flux de travail |
| $schema | Uniquement en cas de référence externe à une définition de flux de travail | Emplacement du fichier de schéma JSON qui décrit la version du langage de définition de flux de travail, que vous pouvez trouver ici : <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |
| contentVersion | Non  | Numéro de version de votre définition de flux de travail (1.0.0.0 par défaut). Pour identifier et vérifier plus facilement la définition correcte lors du déploiement d’un flux de travail, spécifiez une valeur à utiliser. |
| parameters | Non  | Définitions d’un ou plusieurs paramètres qui transmettent des données à votre flux de travail <p><p>Nombre maximal de paramètres : 50 |
| Déclencheurs | Non  | Définitions d’un ou plusieurs déclencheurs qui instancient votre flux de travail. Vous pouvez définir plus d’un déclencheur, mais uniquement avec le langage de définition de flux de travail (vous ne pouvez pas le faire visuellement via le Concepteur Logic Apps). <p><p>Nombre maximal de déclencheurs : 10 |
| actions | Non  | Définitions d’une ou plusieurs actions à exécuter lors de l’exécution du flux de travail <p><p>Nombre maximal d'actions : 250 |
| outputs | Non  | Définitions des sorties renvoyées lors de l’exécution d’un flux de travail <p><p>Nombre maximal de sorties : 10 |
||||

## <a name="parameters"></a>parameters

Dans la section `parameters`, définissez tous les paramètres de flux de travail que votre application logique utilise au moment du déploiement pour accepter les entrées. Les déclarations et les valeurs des paramètres sont requises lors du déploiement. Pour pouvoir utiliser ces paramètres dans d’autres sections du flux de travail, vous devez au préalable déclarer tous les paramètres dans ces sections. 

Voici la structure générale d’une définition de paramètre :

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Élément | Obligatoire | Type | Description |
|---------|----------|------|-------------|
| Type | Oui | int, float, string, securestring, bool, array, objet JSON, secureobject <p><p>**Remarque**: pour tous les mots de passe, les clés et les secrets, utilisez les types `securestring` et `secureobject` car l'opération `GET` ne renvoie pas ces types. | Type du paramètre |
| defaultValue | Non  | Identique à `type` | Valeur par défaut du paramètre quand aucune valeur n’est spécifiée lors de l’instanciation du flux de travail |
| allowedValues | Non  | Identique à `type` | Tableau regroupant les valeurs que le paramètre peut accepter |
| metadata | Non  | Objet JSON | Toutes les autres détails du paramètre, tels que le nom ou une description lisible pour votre application logique, ou les données au moment du design utilisées par Visual Studio ou d’autres outils |
||||

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Dans une définition de flux de travail, les sections `triggers` et `actions` définissent les appels qui se produisent pendant l’exécution de votre flux de travail. Pour obtenir des informations supplémentaires sur ces sections et connaître la syntaxe à utiliser, consultez [Déclencheurs et actions du flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md).

## <a name="outputs"></a>Outputs

Dans la section `outputs`, définissez les données que votre flux de travail peut renvoyer une fois son exécution terminée. Par exemple, pour suivre un état ou une valeur spécifique à chaque exécution, spécifiez le renvoi de cette donnée par la sortie du flux de travail.

> [!NOTE]
> Pour la réponse à des requêtes entrantes à partir de l’API REST d’un service, n’utilisez pas `outputs`. Utilisez le type d’action `Response` à la place. Pour plus d’informations, consultez [Déclencheurs et actions du flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md).

Voici la structure générale d’une définition de sortie :

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Élément | Obligatoire | Type | Description |
|---------|----------|------|-------------|
| <*key-name*> | Oui | Chaîne | Nom de la clé de la valeur renvoyée pour la sortie |
| Type | Oui | int, float, string, securestring, bool, array, objet JSON | Type de la valeur renvoyée pour la sortie |
| value | Oui | Identique à `type` | Valeur renvoyée pour la sortie |
|||||

Pour obtenir la sortie d’une exécution de flux de travail, examinez l’historique et les détails des exécutions de l’application logique dans le portail Azure ou utilisez l’[API REST de flux de travail](https://docs.microsoft.com/rest/api/logic/workflows). Vous pouvez également transmettre la sortie à des systèmes externes, par exemple Power BI, afin de créer des tableaux de bord.

<a name="expressions"></a>

## <a name="expressions"></a>Expressions

Avec JSON, vous pouvez avoir des valeurs littérales qui existent au moment du design, par exemple :

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Vous pouvez également avoir des valeurs qui n’existent pas avant l’exécution. Pour représenter ces valeurs, vous pouvez utiliser des *expressions*, qui sont évaluées au moment de l’exécution. Une expression est une séquence qui peut contenir un ou plusieurs [opérateurs](#operators), [fonctions](#functions), variables, valeurs explicites ou constantes. Dans votre définition de flux de travail, vous pouvez utiliser une expression n’importe où dans une valeur de chaîne JSON en faisant précéder l’expression d’une arobase (\@). Quand une expression qui représente une valeur JSON est évaluée, le corps de l’expression est extrait en supprimant le caractère \@, et une autre valeur JSON est systématiquement générée.

Par exemple, pour la propriété `customerName` définie précédemment, vous pouvez obtenir la valeur de la propriété en utilisant la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) dans une expression de fonction et en assignant cette valeur à la propriété `accountName` :

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

L’*interpolation de chaîne* permet également d’utiliser plusieurs expressions placées entre le caractère \@ et des accolades ({}) à l’intérieur d’une chaîne. Voici la syntaxe :

```json
@{ "<expression1>", "<expression2>" }
```

Le résultat est toujours une chaîne, ce qui rend cette fonctionnalité similaire à la fonction `concat()`, par exemple : 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Si vous avez une chaîne littérale qui commence par le caractère \@, faites précéder le caractère \@ d’un autre caractère \@ faisant office de caractère d’échappement : \@\@

Ces exemples montrent comment les expressions sont évaluées :

| Valeur JSON | Résultat |
|------------|--------|
| "Sophia Owen" | Renvoie les caractères suivants : 'Sophia Owen' |
| "array[1]" | Renvoie ces caractères : « array[1] » |
| "\@\@" | Renvoie ces caractères sous forme de chaîne de un caractère : « \@ » |
| " \@" | Renvoie ces caractères sous forme de chaîne de deux caractères : «  \@ » |
|||

Pour ces exemples, supposons que vous définissiez "January" pour "myBirthMonth" et le nombre 42 pour "myAge" :

```json
"myBirthMonth": "January",
"myAge": 42
```

Ces exemples montrent comment les expressions suivantes sont évaluées :

| Expression JSON | Résultat |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Renvoie la chaîne suivante : "January" |
| "\@{parameters('myBirthMonth')}" | Renvoie la chaîne suivante : "January" |
| "\@parameters('myAge')" | Renvoie le nombre suivant : 42 |
| "\@{parameters('myAge')}" | Renvoie le nombre suivant sous forme de chaîne : "42" |
| "My age is \@{parameters('myAge')}" | Renvoie la chaîne suivante : "My age is 42" |
| "\@concat('My age is ', string(parameters('myAge')))" | Renvoie la chaîne suivante : "My age is 42" |
| "My age is \@\@{parameters('myAge')}" | Renvoie la chaîne suivante, qui inclut l'expression : "My age is \@{parameters('myAge')}` |
|||

Quand vous travaillez visuellement dans le Concepteur Logic Apps, vous pouvez créer des expressions via le Générateur d’expressions, par exemple :

![Concepteur d’applications logiques > Générateur d’expressions](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quand vous avez terminé, l’expression apparaît pour la propriété correspondante dans votre définition de flux de travail, par exemple, la propriété `searchQuery` ici :

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operators

Dans les [expressions](#expressions) et les [fonctions](#functions), les opérateurs effectuent des tâches spécifiques, telles que référencer une propriété ou une valeur dans un tableau.

| Operator | Tâche |
|----------|------|
| ' | Pour utiliser une chaîne littérale en tant qu’entrée ou dans des expressions et des fonctions, vous devez placer la chaîne uniquement entre des guillemets simples, par exemple, `'<myString>'`. N’utilisez pas de guillemets doubles (""), qui entrent en conflit avec le formatage JSON autour d’une expression entière. Par exemple :  <p>**Oui** : length('Hello') </br>**Non** : length("Hello") <p>Quand vous transmettez des tableaux ou des nombres, vous n’avez pas besoin de les placer entre des signes de ponctuation. Par exemple :  <p>**Oui** : length([1, 2, 3]) </br>**Non** : length("[1, 2, 3]") |
| [] | Pour référencer une valeur à une position spécifique (index) dans un tableau, utilisez des crochets. Par exemple, pour obtenir le deuxième élément d’un tableau : <p>`myArray[1]` |
| . | Pour référencer une propriété d’un objet, utilisez l’opérateur point. Par exemple, pour obtenir la propriété `name` d’un objet JSON `customer` : <p>`"@parameters('customer').name"` |
| ? | Pour référencer les propriétés Null d’un objet sans erreur d’exécution, utilisez l’opérateur point d’interrogation. Par exemple, pour gérer les sorties Null d’un déclencheur, vous pouvez utiliser cette expression : <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Fonctions

Certaines expressions obtiennent leurs valeurs à partir d’actions runtime qui peuvent ne pas encore exister au début de l’exécution de l’application logique. Pour référencer ou utiliser ces valeurs dans des expressions, vous pouvez faire appel à des [*fonctions*](../logic-apps/workflow-definition-language-functions-reference.md) fournies par le Langage de définition de workflow.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [actions et déclencheurs du langage de définition de flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md)
* En savoir plus sur la création et la gestion par programmation des applications logiques avec l’[API REST de flux de travail](https://docs.microsoft.com/rest/api/logic/workflows)
