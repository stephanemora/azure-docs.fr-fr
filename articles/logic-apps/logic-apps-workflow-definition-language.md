---
title: Schéma du langage de définition du flux de travail - Azure Logic Apps | Microsoft Docs
description: Écrivez des définitions de flux de travail personnalisées pour Azure Logic Apps avec le langage de définition de flux de travail
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886803"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Définitions de flux de travail Logic Apps avec le langage de définition de flux de travail

Quand vous créez un flux de travail d’application logique avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), la définition sous-jacente de votre flux de travail décrit la logique réelle qui s’exécute pour votre application logique. Cette description suit une structure définie et validée par le schéma de langage de définition de flux de travail, qui utilise le format [JavaScript Objet Notation (JSON)](https://www.json.org/). 
  
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
| Définition | OUI | Élément de départ de votre définition de flux de travail | 
| $schema | Uniquement en cas de référence externe à une définition de flux de travail | Emplacement du fichier de schéma JSON qui décrit la version du langage de définition de flux de travail, que vous pouvez trouver ici : <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Non  | Numéro de version de votre définition de flux de travail (1.0.0.0 par défaut). Pour identifier et vérifier plus facilement la définition correcte lors du déploiement d’un flux de travail, spécifiez une valeur à utiliser. | 
| parameters | Non  | Définitions d’un ou plusieurs paramètres qui transmettent des données à votre flux de travail <p><p>Nombre maximal de paramètres : 50 | 
| Déclencheurs | Non  | Définitions d’un ou plusieurs déclencheurs qui instancient votre flux de travail. Vous pouvez définir plus d’un déclencheur, mais uniquement avec le langage de définition de flux de travail (vous ne pouvez pas le faire visuellement via le Concepteur Logic Apps). <p><p>Nombre maximal de déclencheurs : 10 | 
| actions | Non  | Définitions d’une ou plusieurs actions à exécuter lors de l’exécution du flux de travail <p><p>Nombre maximal d’actions : 250 | 
| outputs | Non  | Définitions des sorties renvoyées lors de l’exécution d’un flux de travail <p><p>Nombre maximal de sorties : 10 |  
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

| Élément | Obligatoire | type | Description |  
|---------|----------|------|-------------|  
| Type | OUI | int, float, string, securestring, bool, array, objet JSON, secureobject <p><p>**Remarque** : pour tous les mots de passe, les clés et les secrets, utilisez les types `securestring` et `secureobject`, car l’opération `GET` ne renvoie pas ces types. | Type du paramètre |
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

| Élément | Obligatoire | type | Description | 
|---------|----------|------|-------------| 
| <*key-name*> | OUI | Chaîne | Nom de la clé de la valeur renvoyée pour la sortie |  
| Type | OUI | int, float, string, securestring, bool, array, objet JSON | Type de la valeur renvoyée pour la sortie | 
| value | OUI | Identique à `type` | Valeur renvoyée pour la sortie |  
||||| 

Pour obtenir la sortie d’une exécution de flux de travail, examinez l’historique et les détails des exécutions de l’application logique dans le portail Azure ou utilisez l’[API REST de flux de travail](https://docs.microsoft.com/rest/api/logic/workflows). Vous pouvez également transmettre la sortie à des systèmes externes, notamment à Power BI pour pouvoir créer des tableaux de bord. 

<a name="expressions"></a>

## <a name="expressions"></a>Expressions

Avec JSON, vous pouvez avoir des valeurs littérales qui existent au moment du design, par exemple :

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Vous pouvez également avoir des valeurs qui n’existent pas avant l’exécution. Pour représenter ces valeurs, vous pouvez utiliser des *expressions*, qui sont évaluées au moment de l’exécution. Une expression est une séquence qui peut contenir un ou plusieurs [opérateurs](#operators), [fonctions](#functions), variables, valeurs explicites ou constantes. Dans votre définition de flux de travail, vous pouvez utiliser une expression n’importe où dans une valeur de chaîne JSON en faisant précéder l’expression d’une arobase (@). Quand une expression qui représente une valeur JSON est évaluée, le corps de l’expression est extrait en supprimant le caractère @, et une autre valeur JSON est systématiquement générée. 

Par exemple, pour la propriété `customerName` définie précédemment, vous pouvez obtenir la valeur de la propriété en utilisant la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) dans une expression de fonction et en assignant cette valeur à la propriété `accountName` :

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

L’*interpolation de chaîne* permet également d’utiliser plusieurs expressions placées entre le caractère @ et des accolades ({}) à l’intérieur d’une chaîne. Voici la syntaxe :

```json
@{ "<expression1>", "<expression2>" }
```

Le résultat est toujours une chaîne, ce qui rend cette fonctionnalité similaire à la fonction `concat()`, par exemple : 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Si vous avez une chaîne littérale qui commence par le caractère @, faites précéder le caractère @ d’un autre caractère @ faisant office de caractère d’échappement : @@.

Ces exemples montrent comment les expressions sont évaluées :

| Valeur JSON | Résultat |
|------------|--------| 
| "Sophia Owen" | Renvoie ces caractères : « Sophia Owen » |
| "array[1]" | Renvoie ces caractères : « array[1] » |
| \«\@\@\» | Renvoie ces caractères sous forme de chaîne de un caractère : « @ » |   
| \« \@» | Renvoie ces caractères sous forme de chaîne de deux caractères : «  @ » |
|||

Pour ces exemples, supposons que vous définissiez "January" pour "myBirthMonth" et le nombre 42 pour "myAge" :  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Ces exemples montrent comment les expressions suivantes sont évaluées :

| Expression JSON | Résultat |
|-----------------|--------| 
| "@parameters('myBirthMonth')" | Renvoie cette chaîne : « January » |  
| "@{parameters('myBirthMonth')}" | Renvoie cette chaîne : « January » |  
| "@parameters('myAge')" | Renvoie ce nombre : 42 |  
| "@{parameters('myAge')}" | Renvoie ce nombre sous forme de chaîne : « 42 » |  
| "My age is @{parameters('myAge')}" | Renvoie cette chaîne : « My age is 42 » |  
| "@concat('My age is ', string(parameters('myAge')))" | Renvoie cette chaîne : « My age is 42 » |  
| "My age is @@{parameters('myAge')}" | Renvoie cette chaîne, qui inclut l’expression : « My age is @{parameters('myAge')} » | 
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

Certaines expressions obtiennent leurs valeurs à partir d’actions runtime qui peuvent ne pas encore exister au début de l’exécution de l’application logique. Pour référencer ou utiliser ces valeurs dans des expressions, vous pouvez faire appel à des [*fonctions*](../logic-apps/workflow-definition-language-functions-reference.md). Par exemple, vous pouvez utiliser des fonctions mathématiques pour effectuer des calculs, telles que la fonction [add()](../logic-apps/workflow-definition-language-functions-reference.md#add), qui renvoie la somme d’entiers ou de nombres à virgule flottante. Pour plus d’informations sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).
Vous pouvez également continuer à découvrir les fonctions et leur usage général.

Voici quelques exemples de tâches que vous pouvez accomplir avec les fonctions : 

| Tâche | Syntaxe de la fonction | Résultat | 
| ---- | --------------- | -------------- | 
| Retourne une chaîne en minuscules. | toLower('<*text*>') <p>Par exemple : toLower('Hello') | "hello" | 
| Renvoyer un identificateur global unique (GUID). | guid() |« c2ecc88d-88c8-4096-912c-d6f2e2b138ce » | 
|||| 

Cet exemple montre comment vous pouvez obtenir la valeur du paramètre `customerName` et affecter cette valeur à la propriété `accountName` en utilisant la fonction [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) dans une expression :

```json
"accountName": "@parameters('customerName')"
```

Voici quelques autres méthodes générales pour utiliser des fonctions dans des expressions :

| Tâche | Syntaxe de la fonction dans une expression | 
| ---- | -------------------------------- | 
| Effectuer une opération avec un élément en transmettant cet élément à une fonction. | "@<*functionName*>(<*item*>)" | 
| 1. Obtenir la valeur de *parameterName* en utilisant la fonction imbriquée `parameters()`. </br>2. Effectuer une opération avec le résultat en transmettant cette valeur à *functionName*. | "@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1. Obtenir le résultat de la fonction interne imbriquée *functionName*. </br>2. Transmettre le résultat à la fonction externe *functionName*. | "@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1. Obtenir le résultat de *functionName*. </br>2. Comme le résultat est un objet avec la propriété *propertyName*, obtenir la valeur de cette propriété. | "@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

Par exemple, la fonction `concat()` peut prendre deux valeurs de chaîne ou plus en tant que paramètres. Cette fonction combine ces chaînes dans une seule chaîne. Vous pouvez transmettre des littéraux de chaîne, par exemple « Sophia » et « Owen », afin d’obtenir la chaîne combinée « SophiaOwen » :

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vous pouvez également obtenir les valeurs de chaîne à partir des paramètres. Cet exemple utilise la fonction `parameters()` dans chaque paramètre `concat()`, ainsi que les paramètres `firstName` et `lastName`. Vous transmettez ensuite les chaînes résultantes à la fonction `concat()` afin d’obtenir une chaîne combinée, par exemple « SophiaOwen » :

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Dans les deux cas, le résultat est affecté à la propriété `customerName`. 

Pour plus d’informations sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).
Vous pouvez également continuer à découvrir l’usage général des fonctions.

<a name="string-functions"></a>

### <a name="string-functions"></a>Fonctions de chaîne

Pour travailler avec des chaînes, vous pouvez utiliser ces fonctions de chaîne, ainsi que certaines [fonctions de collection](#collection-functions). Les fonctions de chaîne sont uniquement utilisables sur des chaînes. 

| Fonction de chaîne | Tâche | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combine au moins deux chaînes et retourne la chaîne combinée. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Vérifier si une chaîne se termine par la sous-chaîne spécifiée. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Générer un identificateur global unique (GUID) sous forme de chaîne. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Renvoyer la position de départ d’une sous-chaîne. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Renvoyer la position de fin d’une sous-chaîne. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Remplacer une sous-chaîne par la chaîne spécifiée et renvoyer la chaîne mise à jour. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Renvoyer un tableau qui comporte tous les caractères d’une chaîne et sépare chaque caractère à l’aide du caractère délimiteur spécifié. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Vérifie si une chaîne commence par une sous-chaîne spécifique. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Renvoyer les caractères d’une chaîne, en commençant à partir de la position spécifiée. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retourne une chaîne en minuscules. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retourne une chaîne en majuscules. | 
| [découper](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Supprime les espaces blancs de début et de fin d’une chaîne et retourne la chaîne mise à jour. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Fonctions de collection

Pour travailler avec des collections, généralement des tableaux, des chaînes et parfois, des dictionnaires, vous pouvez utiliser ces fonctions de collection. 

| Fonction de collection | Tâche | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Vérifie si une collection contient un élément spécifique. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Vérifie si une collection est vide. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Renvoyer le premier élément d’une collection. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retourne une collection qui contient *uniquement* les éléments communs aux collections spécifiées. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Renvoyer une chaîne qui contient *tous* les éléments d’un tableau, séparés par le caractère spécifié. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retourne le dernier élément d’une collection. | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Renvoyer le nombre d’éléments d’une chaîne ou d’un tableau. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Supprime des éléments du début d’une collection et retourne *tous les autres* éléments. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Retourne des éléments du début d’une collection. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retourne une collection qui contient *tous* les éléments des collections spécifiées. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Fonctions de comparaison

Pour travailler avec des conditions, comparer des valeurs et les résultats d’expressions ou évaluer différents types de logique, vous pouvez utiliser ces fonctions de comparaison. Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction de comparaison | Tâche | 
| ------------------- | ---- | 
| [et](../logic-apps/workflow-definition-language-functions-reference.md#and) | Vérifie si toutes les expressions sont vraies. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Vérifier si les deux valeurs sont équivalentes. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Vérifie si la première valeur est supérieure à la seconde. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Vérifie si la première valeur est supérieure ou égale à la seconde. | 
| [si](../logic-apps/workflow-definition-language-functions-reference.md#if) | Vérifie si une expression est vraie ou fausse. En fonction du résultat, retourne une valeur spécifiée. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Vérifie si la première valeur est inférieure à la seconde. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Vérifie si la première valeur est inférieure ou égale à la seconde. | 
| [non](../logic-apps/workflow-definition-language-functions-reference.md#not) | Vérifie si une expression est fausse. | 
| [ou](../logic-apps/workflow-definition-language-functions-reference.md#or) | Vérifie si au moins une expression est vraie. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Fonctions de conversion

Pour modifier le type ou le format d’une valeur, vous pouvez utiliser ces fonctions de conversion. Par exemple, vous pouvez convertir une valeur booléenne en entier. Pour savoir comment Logic Apps gère les types de contenu lors de la conversion, consultez [Gérer les types de contenu](../logic-apps/logic-apps-content-type.md). Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction de conversion | Tâche | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retourne un tableau à partir d’une entrée spécifique unique. Pour des entrées multiples, consultez [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retourne la version encodée en Base64 d’une chaîne. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retourne la version binaire d’une chaîne encodée en Base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Renvoyer la version de type chaîne d’une chaîne encodée en base 64. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Renvoyer la version binaire d’une valeur d’entrée. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Renvoyer la version booléenne d’une valeur d’entrée. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retourne un tableau à partir de plusieurs entrées. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Renvoyer l’URI de données d’une valeur d’entrée. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Renvoyer la version binaire d’un URI de données. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Renvoyer la version de type chaîne d’un URI de données. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Renvoyer la version de type chaîne d’une chaîne encodée en base 64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Renvoyer la version binaire d’un URI de données. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Renvoyer une chaîne qui remplace les caractères d’échappement par des versions décodées. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Renvoyer une chaîne qui remplace les caractères non sécurisés pour les URL par des caractères d'échappement. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Renvoyer un nombre à virgule flottante pour une valeur d’entrée. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retourne la version de type entier d’une chaîne. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retourne la valeur ou l’objet de type JavaScript Object Notation (JSON) d’une chaîne ou d’un élément XML. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Renvoyer la version de type chaîne d’une valeur d’entrée. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Renvoyer la version encodée dans un URI d’une valeur d’entrée en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Renvoyer la version binaire d’une chaîne encodée dans un URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Renvoyer la version de type chaîne d’une chaîne encodée dans un URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Renvoyer la version de type entier d’une chaîne. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Fonctions mathématiques

Pour travailler avec des entiers et des nombres à virgule flottante, vous pouvez utiliser ces fonctions mathématiques. Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction mathématique | Tâche | 
| ------------- | ---- | 
| [ajouter](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retourne le résultat de l’addition de deux nombres. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Renvoyer le résultat de la division de deux nombres. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Renvoyer la valeur la plus élevée d’un ensemble de nombres ou d’un tableau. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retourne la plus petite valeur d’un ensemble de nombres ou d’un tableau. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retourne le reste de la division de deux nombres. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retourne le produit de la multiplication de deux nombres. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Renvoyer un entier aléatoire à partir d’une plage spécifique. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retourne un tableau d’entiers qui commence par un entier spécifique. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retourne le résultat de la soustraction du second nombre du premier. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Fonctions de date et heure

Pour travailler avec des dates et des heures, vous pouvez utiliser ces fonctions de date et heure.
Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction de date ou heure | Tâche | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Ajouter un nombre de jours à un timestamp. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Ajouter un nombre d’heures à un timestamp. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Ajouter un nombre de minutes à un timestamp. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Ajouter un nombre de secondes à un timestamp. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Ajouter un nombre d’unités de temps à un timestamp. Voir aussi [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Convertir un timestamp du temps universel coordonné (UTC) au fuseau horaire cible. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Convertir un timestamp du fuseau horaire source au fuseau horaire cible. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Convertir un timestamp du fuseau horaire source au temps universel coordonné (UTC). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Renvoyer le jour du composant mois d’un timestamp. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Renvoyer le jour du composant semaine d’un timestamp. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Renvoyer le jour du composant année d’un timestamp. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Renvoyer la date d’un timestamp. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Renvoyer le timestamp actuel plus les unités de temps spécifiées. Voir aussi [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Renvoyer le timestamp actuel moins les unités de temps spécifiées. Voir aussi [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Renvoyer le début du jour pour un timestamp. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Renvoyer le début de l’heure pour un timestamp. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Renvoyer le début du mois pour un timestamp. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Soustraire un nombre d’unités de temps d’un timestamp. Voir aussi [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Renvoyer la valeur de la propriété `ticks` pour un timestamp spécifique. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Renvoyer le timestamp actuel sous forme de chaîne. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Fonctions de flux de travail

Grâce à ces fonctions de flux de travail, vous pouvez :

* Obtenir des informations sur une instance de flux de travail au moment de l’exécution 
* Travailler avec les entrées utilisées pour l’instanciation des applications logiques
* Référencer les sorties de déclencheurs et d’actions

Par exemple, vous pouvez référencer les sorties d’une action et utiliser ces données lors d’une action ultérieure. Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction de flux de travail | Tâche | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Renvoyer la sortie de l’action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Voir aussi [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Renvoyer la sortie `body` d’une action lors de l’exécution. Voir aussi [body](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Renvoyer la sortie d’une action lors de l’exécution. Voir [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Renvoyer la sortie d’une action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Voir aussi [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Retourne la sortie `body` d’une action lors de l’exécution. Voir aussi [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Créer un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Renvoyer une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded output* d’une action. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | À l’intérieur d’une action répétée sur un tableau, renvoyer l’élément actuel du tableau au cours de l’itération actuelle de l’action. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | À l’intérieur d’une boucle for-each ou do-until, renvoyer l’élément actuel de la boucle spécifiée.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Renvoyer l’« URL de rappel » qui appelle un déclencheur ou une action. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Renvoyer le corps correspondant à une partie spécifique de la sortie d’une action qui comporte plusieurs parties. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Renvoyer la valeur d’un paramètre décrit dans la définition de votre application logique. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Renvoyer la sortie d’un déclencheur lors de l’exécution ou d’autres paires nom-valeur JSON. Voir aussi [triggerOutputs](#triggerOutputs) et [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Renvoyer la sortie `body` d’un déclencheur lors de l’exécution. Voir [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Renvoyer une valeur unique correspondant à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Renvoyer le corps d’une partie spécifiée dans la sortie en plusieurs parties d’un déclencheur. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Créer un tableau dont les valeurs correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Renvoyer la sortie d’un déclencheur lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Voir [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Renvoyer la valeur d’une variable spécifiée. | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Renvoyer tous les détails sur le flux de travail proprement dit pendant l’exécution. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Fonctions d’analyse d’URI

Pour travailler avec des URI (Uniform Resource Identifier) et obtenir différentes valeurs de propriétés pour ces URI, vous pouvez utiliser ces fonctions d’analyse d’URI. Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction d’analyse d’URI | Tâche | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Renvoyer la valeur `host` pour un URI (Uniform Resource Identifier). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Renvoyer la valeur `path` pour un URI (Uniform Resource Identifier). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Renvoyer les valeurs `path` et `query` pour un URI (Uniform Resource Identifier). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Renvoyer la valeur `port` pour un URI (Uniform Resource Identifier). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Renvoyer la valeur `query` pour un URI (Uniform Resource Identifier). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Renvoyer la valeur `scheme` pour un URI (Uniform Resource Identifier). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Fonctions JSON et XML

Pour travailler avec des objets JSON et des nœuds XML, vous pouvez utiliser ces fonctions de manipulation. Pour obtenir des informations complètes sur chaque fonction, consultez l’[article de référence alphabétique](../logic-apps/workflow-definition-language-functions-reference.md).

| Fonction de manipulation | Tâche | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Ajouter une propriété et sa valeur, ou paire nom-valeur, à un objet JSON et renvoyer l’objet mis à jour. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retourne la première valeur autre que Null d’un ou plusieurs paramètres. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Supprimer une propriété d’un objet JSON et renvoyer l’objet mis à jour. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Définir la valeur d’une propriété d’un objet JSON et renvoyer l’objet mis à jour. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Vérifie si le code XML contient des valeurs ou des nœuds qui correspondent à une expression de langage XPath et retourne les valeurs ou les nœuds correspondants. | 
||| 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [actions et déclencheurs du langage de définition de flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md)
* En savoir plus sur la création et la gestion par programmation des applications logiques avec l’[API REST de flux de travail](https://docs.microsoft.com/rest/api/logic/workflows)
