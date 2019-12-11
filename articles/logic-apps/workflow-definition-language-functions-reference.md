---
title: Guide de référence pour les fonctions dans les expressions
description: Guide de référence sur les fonctions dans les expressions pour Azure Logic Apps et Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: e449eed0a8ae9ed6e1847ea70db6968bf9ede9bd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784525"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Guide de référence sur l’utilisation des fonctions dans les expressions pour Azure Logic Apps et Power Automate

Pour les définitions de flux de travail dans [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Power Automate](https://docs.microsoft.com/flow/getting-started), certaines [expressions](../logic-apps/logic-apps-workflow-definition-language.md#expressions) obtiennent leurs valeurs à partir d’actions runtime qui peuvent ne pas encore exister au début de l’exécution de votre flux de travail. Pour référencer ou traiter ces valeurs dans ces expressions, vous pouvez faire appel à des *fonctions* fournies par le [Langage de définition de workflow](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Cette page de référence s’applique à Azure Logic Apps et à Power Automate, mais apparaît dans la documentation Azure Logic Apps. Bien que cette page se rapporte spécifiquement aux applications logiques, ces fonctions fonctionnement pour les flux et les applications logiques. Pour plus d’informations sur les fonctions et expressions dans Power Automate, consultez [Utiliser des expressions dans des conditions](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Par exemple, vous pouvez calculer des valeurs à l’aide de fonctions mathématiques, telles que la [fonction add()](../logic-apps/workflow-definition-language-functions-reference.md#add), lorsque vous souhaitez la somme d’entiers ou de nombres décimaux. Voici d’autres exemples de tâches que vous pouvez réaliser avec les fonctions :

| Tâche | Syntaxe de la fonction | Résultat |
| ---- | --------------- | ------ |
| Retourne une chaîne en minuscules. | toLower('<*text*>') <p>Par exemple : toLower('Hello') | "hello" |
| Renvoyer un identificateur global unique (GUID). | guid() |« c2ecc88d-88c8-4096-912c-d6f2e2b138ce » |
||||

Pour rechercher des fonctions [selon leur usage général](#ordered-by-purpose), examinez les tables suivantes. Ou, pour plus d’informations sur chaque fonction, consultez la [liste alphabétique](#alphabetical-list).

> [!NOTE]
> Dans la syntaxe des définitions de paramètres, un point d’interrogation (?) affiché après un paramètre signifie que ce paramètre est facultatif.
> Par exemple, consultez [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Fonctions dans les expressions

Pour vous montrer comment utiliser une fonction dans une expression, cet exemple montre comment vous pouvez obtenir la valeur du paramètre `customerName` et affecter cette valeur à la propriété `accountName` en utilisant la fonction [parameters()](#parameters) dans une expression :

```json
"accountName": "@parameters('customerName')"
```

Voici quelques autres méthodes générales pour utiliser des fonctions dans des expressions :

| Tâche | Syntaxe de la fonction dans une expression |
| ---- | -------------------------------- |
| Effectuer une opération avec un élément en transmettant cet élément à une fonction. | "\@<*functionName*>(<*élément*>)" |
| 1. Obtenir la valeur de *parameterName* en utilisant la fonction imbriquée `parameters()`. </br>2. Effectuer une opération avec le résultat en transmettant cette valeur à *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Obtenir le résultat de la fonction interne imbriquée *functionName*. </br>2. Transmettre le résultat à la fonction externe *functionName*. | "\@<*functionName2*>(<*functionName*>(<*élément*>))" |
| 1. Obtenir le résultat de *functionName*. </br>2. Comme le résultat est un objet avec la propriété *propertyName*, obtenir la valeur de cette propriété. | "\@<*functionName*>(<*élément*>).<*propertyName*>" |
|||

Par exemple, la fonction `concat()` peut prendre deux valeurs de chaîne ou plus en tant que paramètres. Cette fonction combine ces chaînes dans une seule chaîne.
Vous pouvez transmettre des littéraux de chaîne, par exemple « Sophia » et « Owen », afin d’obtenir la chaîne combinée « SophiaOwen » :

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vous pouvez également obtenir les valeurs de chaîne à partir des paramètres. Cet exemple utilise la fonction `parameters()` dans chaque paramètre `concat()`, ainsi que les paramètres `firstName` et `lastName`. Vous transmettez ensuite les chaînes résultantes à la fonction `concat()` afin d’obtenir une chaîne combinée, par exemple « SophiaOwen » :

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Dans les deux cas, le résultat est affecté à la propriété `customerName`.

Voici les fonctions disponibles triées par usage général, ou vous pouvez parcourir les fonctions triées par [ordre alphabétique](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Fonctions de chaîne

Pour travailler avec des chaînes, vous pouvez utiliser ces fonctions de chaîne, ainsi que certaines [fonctions de collection](#collection-functions).
Les fonctions de chaîne sont uniquement utilisables sur des chaînes.

| Fonction de chaîne | Tâche |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combine au moins deux chaînes et retourne la chaîne combinée. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Vérifier si une chaîne se termine par la sous-chaîne spécifiée. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Générer un identificateur global unique (GUID) sous forme de chaîne. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Renvoyer la position de départ d’une sous-chaîne. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retourne la position de départ de la dernière occurrence d’une sous-chaîne. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Remplacer une sous-chaîne par la chaîne spécifiée et renvoyer la chaîne mise à jour. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retourne un tableau qui contient des sous-chaînes, séparées par des virgules, extraites à partir d’une chaîne plus grande sur la base d’un caractère délimiteur spécifié dans la chaîne d’origine. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Vérifie si une chaîne commence par une sous-chaîne spécifique. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Renvoyer les caractères d’une chaîne, en commençant à partir de la position spécifiée. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retourne une chaîne en minuscules. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retourne une chaîne en majuscules. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Supprime les espaces blancs de début et de fin d’une chaîne et retourne la chaîne mise à jour. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Fonctions de collection

Pour travailler avec des collections, généralement des tableaux, des chaînes et parfois, des dictionnaires, vous pouvez utiliser ces fonctions de collection.

| Fonction de collection | Tâche |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Vérifie si une collection contient un élément spécifique. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Vérifie si une collection est vide. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Renvoyer le premier élément d’une collection. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retourne une collection qui contient *uniquement* les éléments communs aux collections spécifiées. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | À l’intérieur d’une action répétée sur un tableau, renvoyer l’élément actuel du tableau au cours de l’itération actuelle de l’action. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Renvoyer une chaîne qui contient *tous* les éléments d’un tableau, séparés par le caractère spécifié. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retourne le dernier élément d’une collection. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Renvoyer le nombre d’éléments d’une chaîne ou d’un tableau. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Supprime des éléments du début d’une collection et retourne *tous les autres* éléments. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Retourne des éléments du début d’une collection. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retourne une collection qui contient *tous* les éléments des collections spécifiées. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Fonctions de comparaison logiques

Pour travailler avec des conditions, comparer des valeurs et des résultats d’expressions, ou évaluer différents types de logique, vous pouvez utiliser ces fonctions de comparaison logiques.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Fonction de comparaison logique | Tâche |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Vérifie si toutes les expressions sont vraies. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Vérifier si les deux valeurs sont équivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Vérifie si la première valeur est supérieure à la seconde. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Vérifie si la première valeur est supérieure ou égale à la seconde. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Vérifie si une expression est vraie ou fausse. En fonction du résultat, retourne une valeur spécifiée. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Vérifie si la première valeur est inférieure à la seconde. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Vérifie si la première valeur est inférieure ou égale à la seconde. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Vérifie si une expression est fausse. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Vérifie si au moins une expression est vraie. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Fonctions de conversion

Pour modifier le type ou le format d’une valeur, vous pouvez utiliser ces fonctions de conversion.
Par exemple, vous pouvez convertir une valeur booléenne en entier.
Pour plus d’informations sur la manière dont Logic Apps gère les types de contenu lors de la conversion, consultez [Gérer les types de contenu](../logic-apps/logic-apps-content-type.md).
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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

## <a name="math-functions"></a>Fonctions mathématiques

Pour travailler avec des entiers et des nombres à virgule flottante, vous pouvez utiliser ces fonctions mathématiques.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Fonction mathématique | Tâche |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retourne le résultat de l’addition de deux nombres. |
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

## <a name="date-and-time-functions"></a>Fonctions de date et heure

Pour travailler avec des dates et des heures, vous pouvez utiliser ces fonctions de date et heure.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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

## <a name="workflow-functions"></a>Fonctions de flux de travail

Grâce à ces fonctions de flux de travail, vous pouvez :

* Obtenir des informations sur une instance de flux de travail au moment de l’exécution
* Travaillez avec les entrées utilisées pour l’instanciation de flux ou applications logiques.
* Référencer les sorties de déclencheurs et d’actions

Par exemple, vous pouvez référencer les sorties d’une action et utiliser ces données lors d’une action ultérieure.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Fonction de flux de travail | Tâche |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Renvoyer la sortie de l’action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Voir aussi [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Renvoyer la sortie `body` d’une action lors de l’exécution. Voir aussi [body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Retourne la sortie d’une action lors de l’exécution. Consultez les [sorties](../logic-apps/workflow-definition-language-functions-reference.md#outputs) et les [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Renvoyer la sortie d’une action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Voir aussi [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Retourne la sortie `body` d’une action lors de l’exécution. Voir aussi [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Créer un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Renvoyer une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded output* d’une action. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | À l’intérieur d’une action répétée sur un tableau, renvoyer l’élément actuel du tableau au cours de l’itération actuelle de l’action. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | À l’intérieur d’une boucle Foreach ou Until, retourne l’élément actuel de la boucle spécifiée.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | À l’intérieur d’une boucle Until, retourne la valeur d’index de l’itération actuelle. Vous pouvez utiliser cette fonction à l’intérieur de boucles Until imbriquées. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Renvoyer l’« URL de rappel » qui appelle un déclencheur ou une action. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Renvoyer le corps correspondant à une partie spécifique de la sortie d’une action qui comporte plusieurs parties. |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Retourne la sortie d’une action lors de l’exécution. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retourne la valeur d’un paramètre décrit dans la définition de votre flux de travail. |
| [result](../logic-apps/workflow-definition-language-functions-reference.md#result) | Retourne les entrées et les sorties de toutes les actions à l’intérieur de l’action délimitée spécifiée, comme `For_each`, `Until` et `Scope`. |
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

## <a name="uri-parsing-functions"></a>Fonctions d’analyse d’URI

Pour travailler avec des URI (Uniform Resource Identifier) et obtenir différentes valeurs de propriétés pour ces URI, vous pouvez utiliser ces fonctions d’analyse d’URI.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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

## <a name="manipulation-functions-json--xml"></a>Fonctions de manipulation : JSON et XML

Pour travailler avec des objets JSON et des nœuds XML, vous pouvez utiliser ces fonctions de manipulation.
Pour obtenir des informations complètes sur chaque fonction, consultez la [liste alphabétique](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Fonction de manipulation | Tâche |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Ajouter une propriété et sa valeur, ou paire nom-valeur, à un objet JSON et renvoyer l’objet mis à jour. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retourne la première valeur autre que Null d’un ou plusieurs paramètres. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Supprimer une propriété d’un objet JSON et renvoyer l’objet mis à jour. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Définir la valeur d’une propriété d’un objet JSON et renvoyer l’objet mis à jour. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Vérifie si le code XML contient des valeurs ou des nœuds qui correspondent à une expression de langage XPath et retourne les valeurs ou les nœuds correspondants. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Toutes les fonctions (liste alphabétique)

Cette section répertorie toutes les fonctions disponibles par ordre alphabétique.

<a name="action"></a>

### <a name="action"></a>action

Retourne la sortie de l’action *en cours* lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression.
Par défaut, cette fonction fait référence à l’objet d’action dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur.
Voir aussi [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Vous pouvez utiliser la fonction `action()` uniquement dans les emplacements suivants :

* La propriété `unsubscribe` pour une action de Webhook afin de pouvoir accéder au résultat de la demande `subscribe` d’origine
* La propriété `trackedProperties` d’une action
* La condition de boucle `do-until` d’une action

```
action()
action().outputs.body.<property>
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*property*> | Non | Chaîne | Nom de propriété de l’objet d’action dont vous souhaitez la valeur : **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** et **clientTrackingId**. Vous trouverez ces propriétés dans le portail Azure en passant en revue les détails d’un historique des exécutions spécifique. Pour plus d’informations, consultez [API REST - Actions d’exécution du workflow](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*action-output*> | Chaîne | Sortie de la propriété ou de l’action en cours |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Retourne la sortie `body` d’une action lors de l’exécution.
Raccourci de `actions('<actionName>').outputs.body`.
Voir [body()](#body) et [actions()](#actions).

```
actionBody('<actionName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de la sortie `body` de l’action souhaitée |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*action-body-output*> | Chaîne | Sortie `body` de l’action spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de la sortie `body` de l’action Twitter `Get user` :

```
actionBody('Get_user')
```

Et retourne ce résultat :

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Retourne la sortie d’une action lors de l’exécution.  Raccourci de `actions('<actionName>').outputs`. Voir [actions()](#actions). La fonction `actionOutputs()` est résolue en`outputs()` dans le concepteur d’application logique. Pensez donc à utiliser [outputs()](#outputs), plutôt que `actionOutputs()`. Bien que les deux fonctions agissent de manière identique, la fonction `outputs()` est préférée.

```
actionOutputs('<actionName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de la sortie de l’action souhaitée |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*output*> | Chaîne | Sortie de l’action spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de la sortie de l’action Twitter `Get user` :

```
actionOutputs('Get_user')
```

Et retourne ce résultat :

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>actions

Retourne la sortie d’une action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression. Par défaut, la fonction fait référence à l’objet d’action dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur.
Pour les versions raccourcies, consultez [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) et [body()](#body).
Pour l’action actuelle, consultez [action()](#action).

> [!NOTE]
> Auparavant, vous pouviez utiliser la fonction `actions()` ou l’élément `conditions` pour spécifier qu’une action était exécutée en fonction de la sortie d’une autre action. Toutefois, pour déclarer explicitement des dépendances entre des actions, vous devez maintenant utiliser la propriété `runAfter` de l’action dépendante.
> Pour en savoir plus sur la propriété `runAfter`, consultez [Intercepter et gérer les échecs avec la propriété runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de l’objet d’action dont vous souhaitez la sortie  |
| <*property*> | Non | Chaîne | Nom de propriété de l’objet d’action dont vous souhaitez la valeur : **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** et **clientTrackingId**. Vous trouverez ces propriétés dans le portail Azure en passant en revue les détails d’un historique des exécutions spécifique. Pour plus d’informations, consultez [API REST - Actions d’exécution du workflow](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*action-output*> | Chaîne | Sortie de l’action ou de la propriété spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de la valeur de la propriété `status` de l’action Twitter `Get user` lors de l’exécution :

```
actions('Get_user').outputs.body.status
```

Et retourne ce résultat : `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Retourne le résultat de l’addition de deux nombres.

```
add(<summand_1>, <summand_2>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | OUI | Entier, flottant ou mixte | Nombres à ajouter |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*result-sum*> | Entier ou flottant | Résultat de l’addition des nombres spécifiés |
||||

*Exemple*

Cet exemple illustre l’addition des nombres spécifiés :

```
add(1, 1.5)
```

Et retourne ce résultat : `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Ajoute un nombre de jours à un horodatage.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*days*> | OUI | Integer | Nombre de jours positif ou négatif à ajouter |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage plus le nombre de jours spécifié  |
||||

*Exemple 1*

Cet exemple illustre l’ajout de 10 jours à l’horodatage spécifié :

```
addDays('2018-03-15T13:00:00Z', 10)
```

Et retourne ce résultat : `"2018-03-25T00:00:0000000Z"`

*Exemple 2*

Cet exemple illustre le retrait de cinq jours de l’horodatage spécifié :

```
addDays('2018-03-15T00:00:00Z', -5)
```

Et retourne ce résultat : `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Ajoute un nombre d’heures à un horodatage.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*hours*> | OUI | Integer | Nombre d’heures positif ou négatif à ajouter |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage plus le nombre d’heures spécifié  |
||||

*Exemple 1*

Cet exemple illustre l’ajout de 10 heures à l’horodatage spécifié :

```
addHours('2018-03-15T00:00:00Z', 10)
```

Et retourne ce résultat : `"2018-03-15T10:00:0000000Z"`

*Exemple 2*

Cet exemple illustre le retrait de cinq heures de l’horodatage spécifié :

```
addHours('2018-03-15T15:00:00Z', -5)
```

Et retourne ce résultat : `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Ajoute un nombre de minutes à un horodatage.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*minutes*> | OUI | Integer | Nombre de minutes positif ou négatif à ajouter |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage plus le nombre de minutes spécifié |
||||

*Exemple 1*

Cet exemple illustre l’ajout de 10 minutes à l’horodatage spécifié :

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Et retourne ce résultat : `"2018-03-15T00:20:00.0000000Z"`

*Exemple 2*

Cet exemple illustre le retrait de cinq minutes de l’horodatage spécifié :

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Et retourne ce résultat : `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Ajouter une propriété et sa valeur, ou paire nom-valeur, à un objet JSON et renvoyer l’objet mis à jour. Si la propriété existe déjà lors de l’exécution, la fonction échoue et génère une erreur.

```
addProperty(<object>, '<property>', <value>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dans lequel vous souhaitez ajouter une propriété |
| <*property*> | OUI | Chaîne | Nom de la propriété à ajouter |
| <*value*> | OUI | Quelconque | Valeur de la propriété |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Objet JSON mis à jour avec la propriété spécifiée |
||||

Pour ajouter une propriété enfant à une propriété existante, utilisez cette syntaxe :

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dans lequel vous souhaitez ajouter une propriété |
| <*parent-property*> | OUI | Chaîne | Nom de la propriété parente où vous voulez ajouter la propriété enfant |
| <*child-property*> | OUI | Chaîne | Nom de la propriété enfant à ajouter |
| <*value*> | OUI | Quelconque | Valeur à définir pour la propriété spécifiée |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Objet JSON mis à jour dont vous définissez la propriété |
||||

*Exemple 1*

Cet exemple ajoute la propriété `middleName` à un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json). L’objet contient déjà les propriétés `firstName` et `surName`. La fonction affecte la valeur spécifiée à la nouvelle propriété et retourne l’objet mis à jour :

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Voici l’objet JSON actuel :

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Voici l’objet JSON mis à jour :

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exemple 2*

Cet exemple ajoute la propriété enfant `middleName` à la propriété `customerName` existante dans un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json). La fonction affecte la valeur spécifiée à la nouvelle propriété et retourne l’objet mis à jour :

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Voici l’objet JSON actuel :

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Voici l’objet JSON mis à jour :

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Ajoute un nombre de secondes à un horodatage.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*seconds*> | OUI | Integer | Nombre de secondes positif ou négatif à ajouter |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage plus le nombre de secondes spécifié  |
||||

*Exemple 1*

Cet exemple illustre l’ajout de 10 secondes à l’horodatage spécifié :

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Et retourne ce résultat : `"2018-03-15T00:00:10.0000000Z"`

*Exemple 2*

Cet exemple illustre le retrait de cinq secondes de l’horodatage spécifié :

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Et retourne ce résultat : `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Ajoute un nombre d’unités de temps à un horodatage.
Voir aussi [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*interval*> | OUI | Integer | Nombre d’unités de temps spécifiées à ajouter |
| <*timeUnit*> | OUI | Chaîne | L’unité de temps à utiliser avec *interval* : "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage plus nombre d’unités de temps spécifié  |
||||

*Exemple 1*

Cet exemple illustre l’ajout d’un jour à l’horodatage spécifié :

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Et retourne ce résultat : `"2018-01-02T00:00:00.0000000Z"`

*Exemple 2*

Cet exemple illustre l’ajout d’un jour à l’horodatage spécifié :

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Et retourne le résultat en utilisant le format « D » facultatif : `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

Vérifie si toutes les expressions sont vraies.
Retourne la valeur true lorsque toutes les expressions sont vraies ou la valeur false lorsque au moins une expression est fausse.

```
and(<expression1>, <expression2>, ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>... | OUI | Boolean | Expressions à vérifier |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| true ou false | Boolean | Retourne la valeur true si toutes les expressions sont vraies. Retourne la valeur false si au moins une expression est fausse. |
||||

*Exemple 1*

Ces exemples vérifient si toutes les valeurs booléennes spécifiées sont vraies :

```
and(true, true)
and(false, true)
and(false, false)
```

Et retournent les résultats suivants :

* Premier exemple : comme les deux expressions sont vraies, la valeur `true` est retournée.
* Deuxième exemple : comme une des expressions est fausse, la valeur `false` est retournée.
* Troisième exemple : comme les deux expressions sont fausses, la valeur `false` est retournée.

*Exemple 2*

Ces exemples vérifient si toutes les expressions spécifiées sont vraies :

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Et retournent les résultats suivants :

* Premier exemple : comme les deux expressions sont vraies, la valeur `true` est retournée.
* Deuxième exemple : comme une des expressions est fausse, la valeur `false` est retournée.
* Troisième exemple : comme les deux expressions sont fausses, la valeur `false` est retournée.

<a name="array"></a>

### <a name="array"></a>array

Retourne un tableau à partir d’une entrée spécifique unique.
Pour des entrées multiples, consultez [createArray()](#createArray).

```
array('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne permettant de créer un tableau |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*valeur*>] | Array | Tableau qui contient l’entrée spécifiée unique |
||||

*Exemple*

Cet exemple illustre la création d’un tableau à partir de la chaîne « hello » :

```
array('hello')
```

Et retourne ce résultat : `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retourne la version encodée en Base64 d’une chaîne.

```
base64('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne d’entrée |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*base64-string*> | Chaîne | Retourne la version encodée en Base64 de la chaîne d’entrée |
||||

*Exemple*

Cet exemple illustre la conversion d’une chaîne « hello » en chaîne encodée en Base64 :

```
base64('hello')
```

Et retourne ce résultat : `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retourne la version binaire d’une chaîne encodée en Base64.

```
base64ToBinary('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Chaîne | Version binaire de la chaîne encodée en Base64 |
||||

*Exemple*

Cet exemple illustre la conversion d’une chaîne « aGVsbG8= » encodée en Base64 en chaîne binaire :

```
base64ToBinary('aGVsbG8=')
```

Et retourne ce résultat :

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retourne la version de type chaîne d’une chaîne encodée en Base64, en décodant efficacement la chaîne Base64.
Utilisez cette fonction plutôt que la fonction [decodeBase64()](#decodeBase64).
Bien que les deux fonctions agissent de manière identique, la fonction `base64ToString()` est préférée.

```
base64ToString('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à décoder |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Chaîne | Version de type chaîne d’une chaîne encodée en Base64 |
||||

*Exemple*

Cet exemple illustre la conversion d’une chaîne « aGVsbG8= » encodée en Base64 en chaîne :

```
base64ToString('aGVsbG8=')
```

Et retourne ce résultat : `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Retourne la version binaire d’une chaîne.

```
binary('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Chaîne | Version binaire de la chaîne spécifiée |
||||

*Exemple*

Cet exemple illustre la conversion d’une chaîne « hello » en chaîne binaire :

```
binary('hello')
```

Et retourne ce résultat :

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Retourne la sortie `body` d’une action lors de l’exécution.
Raccourci de `actions('<actionName>').outputs.body`.
Voir [actionBody()](#actionBody) et [actions()](#actions).

```
body('<actionName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de la sortie `body` de l’action souhaitée |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*action-body-output*> | Chaîne | Sortie `body` de l’action spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de la sortie `body` de l’action Twitter `Get user` :

```
body('Get_user')
```

Et retourne ce résultat :

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Retourne la version booléenne d’une valeur.

```
bool(<value>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Quelconque | Valeur à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Version booléenne de la valeur spécifiée |
||||

*Exemple*

Ces exemples illustrent la conversion des valeurs spécifiées en valeurs booléennes :

```
bool(1)
bool(0)
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Second exemple : `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Retourne la première valeur autre que Null d’un ou plusieurs paramètres.
Les chaînes vides, les tableaux vides et les objets vides ne sont pas null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>... | OUI | N’importe lequel, possibilité de combiner plusieurs types | Recherche de la valeur Null dans un ou plusieurs éléments |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Quelconque | Premier élément ou valeur autre que Null. Si tous les paramètres ont la valeur Null, cette fonction retourne la valeur Null. |
||||

*Exemple*

Ces exemples retournent la première valeur autre que Null à partir des valeurs spécifiées ou la valeur Null lorsque toutes les valeurs le sont :

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Deuxième exemple : `"hello"`
* Troisième exemple : `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combine au moins deux chaînes et retourne la chaîne combinée.

```
concat('<text1>', '<text2>', ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>... | OUI | Chaîne | Au moins deux chaînes à combiner |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Chaîne | Chaîne créée à partir des chaînes d’entrée combinées |
||||

*Exemple*

Cet exemple illustre la combinaison des chaînes « Hello » et « World » :

```
concat('Hello', 'World')
```

Et retourne ce résultat : `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Vérifie si une collection contient un élément spécifique.
Retourne la valeur true lorsque l’élément est trouvé ou la valeur false lorsqu’il est introuvable.
Cette fonction respecte la casse.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Plus précisément, cette fonction agit sur ces types de collection :

* Une *chaîne* pour y rechercher une *sous-chaîne*
* Un *tableau* pour y rechercher une *valeur*
* Un *ensemble de clés* pour y rechercher une *clé*

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne, tableau ou ensemble de clés | Collection à vérifier |
| <*value*> | OUI | Respectivement, chaîne, tableau ou ensemble de clés | Élément à rechercher |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true lorsque l’élément est trouvé. Retourne la valeur false lorsqu’elle est introuvable. |
||||

*Exemple 1*

Cet exemple recherche dans la chaîne « hello world » la sous-chaîne « world » et retourne la valeur true :

```
contains('hello world', 'world')
```

*Exemple 2*

Cet exemple recherche dans la chaîne « hello world » la sous-chaîne « universe » et retourne la valeur false :

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Convertit un horodatage du temps universel coordonné (UTC) au fuseau horaire cible.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*destinationTimeZone*> | OUI | Chaîne | Nom du fuseau horaire cible. Pour les noms de fuseau horaire, consultez [Valeurs d’index de fuseau horaire Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mais vous devrez peut-être supprimer les signes de ponctuation du nom de fuseau horaire. |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Chaîne | Horodatage converti au fuseau horaire cible |
||||

*Exemple 1*

Cet exemple illustre la conversion d’un horodatage au fuseau horaire spécifié :

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Et retourne ce résultat : `"2018-01-01T00:00:00.0000000"`

*Exemple 2*

Cet exemple illustre la conversion d’un horodatage au fuseau horaire et au format spécifiés :

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Et retourne ce résultat : `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Convertit un horodatage du fuseau horaire source au fuseau horaire cible.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*sourceTimeZone*> | OUI | Chaîne | Nom du fuseau horaire source. Pour les noms de fuseau horaire, consultez [Valeurs d’index de fuseau horaire Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mais vous devrez peut-être supprimer les signes de ponctuation du nom de fuseau horaire. |
| <*destinationTimeZone*> | OUI | Chaîne | Nom du fuseau horaire cible. Pour les noms de fuseau horaire, consultez [Valeurs d’index de fuseau horaire Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mais vous devrez peut-être supprimer les signes de ponctuation du nom de fuseau horaire. |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Chaîne | Horodatage converti au fuseau horaire cible |
||||

*Exemple 1*

Cet exemple illustre la conversion du fuseau horaire source au fuseau horaire cible :

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Et retourne ce résultat : `"2018-01-01T00:00:00.0000000"`

*Exemple 2*

Cet exemple illustre la conversion d’un fuseau horaire au format et au fuseau horaire spécifiés :

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Et retourne ce résultat : `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Convertit un horodatage du fuseau horaire source en temps universel coordonné (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*sourceTimeZone*> | OUI | Chaîne | Nom du fuseau horaire source. Pour les noms de fuseau horaire, consultez [Valeurs d’index de fuseau horaire Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mais vous devrez peut-être supprimer les signes de ponctuation du nom de fuseau horaire. |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Chaîne | Horodatage converti en heure UTC |
||||

*Exemple 1*

Cet exemple illustre la conversion d’un horodatage en heure UTC :

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Et retourne ce résultat : `"2018-01-01T08:00:00.0000000Z"`

*Exemple 2*

Cet exemple illustre la conversion d’un horodatage en heure UTC :

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Et retourne ce résultat : `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retourne un tableau à partir de plusieurs entrées.
Pour les tableaux à entrée unique, consultez [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>... | OUI | N’importe lequel, mais pas de combinaison | Au moins deux éléments pour créer le tableau |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>...] | Array | Tableau créé à partir de tous les éléments d’entrée |
||||

*Exemple*

Cet exemple illustre la création d’un tableau à partir de ces entrées :

```
createArray('h', 'e', 'l', 'l', 'o')
```

Et retourne ce résultat : `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Retourne un URI de données pour une chaîne.

```
dataUri('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*data-uri*> | Chaîne | URI de données de la chaîne d’entrée |
||||

*Exemple*

Cet exemple illustre la création d’un URI de données à partir de la chaîne « hello » :

```
dataUri('hello')
```

Et retourne ce résultat : `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retourne la version binaire d’un URI (Uniform Resource Identifier) de données.
Utilisez cette fonction plutôt que la fonction [decodeDataUri()](#decodeDataUri).
Bien que les deux fonctions agissent de manière identique, la fonction `dataUriBinary()` est préférée.

```
dataUriToBinary('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | URI de données à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Chaîne | Version binaire de l’URI de données |
||||

*Exemple*

Cet exemple illustre la création d’une version binaire pour cet URI de données :

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Et retourne ce résultat :

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retourne la version de type chaîne d’un URI de données.

```
dataUriToString('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | URI de données à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Chaîne | Version de type chaîne de l’URI de données |
||||

*Exemple*

Cet exemple illustre la création d’une chaîne pour cet URI de données :

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Et retourne ce résultat : `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retourne le jour du mois d’un horodatage.

```
dayOfMonth('<timestamp>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | Jour du mois de l’horodatage spécifié |
||||

*Exemple*

Cet exemple retourne le numéro du jour du mois à partir de cet horodatage :

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retourne le jour de la semaine à partir d’un horodatage.

```
dayOfWeek('<timestamp>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | Jour de la semaine à partir de l’horodatage spécifié où dimanche représente 0, lundi représente 1, etc. |
||||

*Exemple*

Cet exemple retourne le numéro du jour de la semaine à partir de cet horodatage :

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retourne le jour de l’année à partir d’un horodatage.

```
dayOfYear('<timestamp>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Jour de l’année à partir de l’horodatage spécifié |
||||

*Exemple*

Cet exemple retourne le numéro du jour de l’année à partir de cet horodatage :

```
dayOfYear('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Retourne la version de type chaîne d’une chaîne encodée en Base64, en décodant efficacement la chaîne Base64.
Pensez à utiliser la fonction [base64ToString()](#base64ToString) plutôt que la fonction `decodeBase64()`.
Bien que les deux fonctions agissent de manière identique, la fonction `base64ToString()` est préférée.

```
decodeBase64('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à décoder |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Chaîne | Version de type chaîne d’une chaîne encodée en Base64 |
||||

*Exemple*

Cet exemple illustre la création d’une chaîne pour une chaîne encodée en Base64 :

```
decodeBase64('aGVsbG8=')
```

Et retourne ce résultat : `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retourne la version binaire d’un URI (Uniform Resource Identifier) de données.
Pensez à utiliser la fonction [dataUriToBinary()](#dataUriToBinary) plutôt que la fonction `decodeDataUri()`.
Bien que les deux fonctions agissent de manière identique, la fonction `dataUriToBinary()` est préférée.

```
decodeDataUri('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne d’URI de données à décoder |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Chaîne | Version binaire d’une chaîne d’URI de données |
||||

*Exemple*

Cet exemple retourne la version binaire de cet URI de données :

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Et retourne ce résultat :

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retourne une chaîne qui remplace les caractères d’échappement par des versions décodées.

```
decodeUriComponent('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne contenant les caractères d’échappement à décoder |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Chaîne | Chaîne mise à jour contenant les caractères d’échappement décodés |
||||

*Exemple*

Cet exemple illustre le remplacement des caractères d’échappement de cette chaîne par des versions décodées :

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Et retourne ce résultat : `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retourne l’entier résultant de la division de deux nombres.
Pour obtenir le reste, consultez [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | OUI | Entier ou flottant | Nombre à diviser par le *diviseur* |
| <*divisor*> | OUI | Entier ou flottant | Nombre qui divise le *dividende*, mais qui ne peut pas être égal à 0 |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | Entier résultant de la division du premier nombre par le second nombre |
||||

*Exemple*

Les deux exemples illustrent la division du premier nombre par le second :

```
div(10, 5)
div(11, 5)
```

Et retourne ce résultat : `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retourne une version encodée sous forme d’URI d’une chaîne en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement.
Pensez à utiliser la fonction [uriComponent()](#uriComponent) plutôt que la fonction `encodeUriComponent()`.
Bien que les deux fonctions agissent de manière identique, la fonction `uriComponent()` est préférée.

```
encodeUriComponent('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne à convertir au format encodé sous forme d’URI |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Chaîne | Chaîne encodée sous forme d’URI avec des caractères d’échappement |
||||

*Exemple*

Cet exemple illustre la création d’une version encodée sous forme d’URI pour cette chaîne :

```
encodeUriComponent('https://contoso.com')
```

Et retourne ce résultat : `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Vérifie si une collection est vide.
Retourne la valeur true lorsque la collection est vide ou la valeur false dans le cas contraire.

```
empty('<collection>')
empty([<collection>])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne, tableau ou objet | Collection à vérifier |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true lorsque la collection est vide. Retourne la valeur false lorsque la collection n’est pas vide. |
||||

*Exemple*

Ces exemples vérifient si les collections spécifiées sont vides :

```
empty('')
empty('abc')
```

Et retournent les résultats suivants :

* Premier exemple : comme une chaîne vide est passée, la fonction retourne `true`.
* Deuxième exemple : comme la chaîne « abc » est passée, la fonction retourne `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Vérifie si une chaîne se termine par une sous-chaîne spécifique.
Retourne la valeur true lorsque la sous-chaîne est trouvée ou la valeur false lorsqu’elle est introuvable.
Cette fonction ne respecte pas la casse.

```
endsWith('<text>', '<searchText>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne à vérifier |
| <*searchText*> | OUI | Chaîne | Sous-chaîne de fin à rechercher |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retourne la valeur true lorsque la sous-chaîne de fin est trouvée. Retourne la valeur false lorsqu’elle est introuvable. |
||||

*Exemple 1*

Cet exemple vérifie si la chaîne « hello world » se termine par la chaîne « world » :

```
endsWith('hello world', 'world')
```

Et retourne ce résultat : `true`

*Exemple 2*

Cet exemple vérifie si la chaîne « hello world » se termine par la chaîne « universe » :

```
endsWith('hello world', 'universe')
```

Et retourne ce résultat : `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Vérifie si les deux valeurs, expressions ou objets sont équivalents.
Retourne la valeur true si les deux sont équivalents ou la valeur false s’ils ne le sont pas.

```
equals('<object1>', '<object2>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | OUI | Divers | Valeurs, expressions ou objets à comparer |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true lorsque les deux sont équivalents. Retourne la valeur false lorsque les deux ne sont pas équivalents. |
||||

*Exemple*

Ces exemples vérifient si les entrées spécifiées sont équivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

Et retournent les résultats suivants :

* Premier exemple : comme les deux valeurs sont équivalentes, la fonction retourne `true`.
* Deuxième exemple : comme les deux valeurs ne sont pas équivalentes, la fonction retourne `false`.

<a name="first"></a>

### <a name="first"></a>first

Retourne le premier élément d’une chaîne ou d’un tableau.

```
first('<collection>')
first([<collection>])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne ou tableau | Collection dans laquelle rechercher le premier élément |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Quelconque | Premier élément de la collection |
||||

*Exemple*

Ces exemples illustrent la recherche du premier élément dans ces collections :

```
first('hello')
first(createArray(0, 1, 2))
```

Et retournent les résultats suivants :

* Premier exemple : `"h"`
* Second exemple : `0`

<a name="float"></a>

### <a name="float"></a>float

Convertit une version de type chaîne d’un nombre à virgule flottante en nombre réel à virgule flottante.
Vous pouvez utiliser cette fonction uniquement lors de la transmission de paramètres personnalisés à une application, telle qu’une application logique ou un flux.

```
float('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne qui contient un nombre valide à virgule flottante à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Nombre à virgule flottante de la chaîne spécifiée |
||||

*Exemple*

Cet exemple illustre la création d’une version de type chaîne pour ce nombre à virgule flottante :

```
float('10.333')
```

Et retourne ce résultat : `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retourne un horodatage au format spécifié.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | Chaîne | Horodatage mis à jour au format spécifié |
||||

*Exemple*

Cet exemple illustre la conversion d’un horodatage au format spécifié :

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Et retourne ce résultat : `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retourne un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action.

```
formDataMultiValues('<actionName>', '<key>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Action dont la sortie contient la valeur de clé souhaitée |
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Tableau contenant toutes les valeurs qui correspondent à la clé spécifiée |
||||

*Exemple*

Cet exemple illustre la création d’un tableau à partir de la valeur de la clé « Subject » dans la sortie form-data ou form-encoded de l’action spécifiée :

```
formDataMultiValues('Send_an_email', 'Subject')
```

Et retourne le texte de l’objet dans un tableau, par exemple : `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Retourne une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action.
Si la fonction trouve plusieurs correspondances, elle génère une erreur.

```
formDataValue('<actionName>', '<key>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Action dont la sortie contient la valeur de clé souhaitée |
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*key-value*> | Chaîne | Valeur de la clé spécifiée  |
||||

*Exemple*

Cet exemple illustre la création d’une chaîne à partir de la valeur de la clé « Subject » dans la sortie form-data ou form-encoded de l’action spécifiée :

```
formDataValue('Send_an_email', 'Subject')
```

Et retourne le texte de l’objet sous forme de chaîne, par exemple : `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retourne l’horodatage actuel plus les unités de temps spécifiées.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | OUI | Integer | Nombre d’unités de temps spécifiées à ajouter |
| <*timeUnit*> | OUI | Chaîne | L’unité de temps à utiliser avec *interval* : "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage actuel plus nombre d’unités de temps spécifié |
||||

*Exemple 1*

Supposez que l’horodatage actuel soit « 2018-03-01T00:00:00.0000000Z ».
Cet exemple illustre l’ajout de cinq jours à cet horodatage :

```
getFutureTime(5, 'Day')
```

Et retourne ce résultat : `"2018-03-06T00:00:00.0000000Z"`

*Exemple 2*

Supposez que l’horodatage actuel soit « 2018-03-01T00:00:00.0000000Z ».
Cet exemple illustre l’ajout de cinq jours et convertit le résultat au format « D » :

```
getFutureTime(5, 'Day', 'D')
```

Et retourne ce résultat : `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retourne l’horodatage actuel moins les unités de temps spécifiées.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | OUI | Integer | Nombre d’unités de temps spécifiées à soustraire |
| <*timeUnit*> | OUI | Chaîne | L’unité de temps à utiliser avec *interval* : "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage actuel moins nombre d’unités de temps spécifié |
||||

*Exemple 1*

Supposez que l’horodatage actuel soit « 2018-02-01T00:00:00.0000000Z ».
Cet exemple illustre le retrait de cinq jours de cet horodatage :

```
getPastTime(5, 'Day')
```

Et retourne ce résultat : `"2018-01-27T00:00:00.0000000Z"`

*Exemple 2*

Supposez que l’horodatage actuel soit « 2018-02-01T00:00:00.0000000Z ».
Cet exemple illustre le retrait de cinq jours et convertit le résultat au format « D » :

```
getPastTime(5, 'Day', 'D')
```

Et retourne ce résultat : `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Vérifie si la première valeur est supérieure à la seconde.
Retourne la valeur true si la première valeur est supérieure ou la valeur false si elle est inférieure.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est supérieure à la seconde |
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Valeur de comparaison |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true si la première valeur est supérieure à la seconde. Retourne la valeur false si la première valeur est égale ou inférieure à la seconde. |
||||

*Exemple*

Ces exemples vérifient si la première valeur est supérieure à la seconde :

```
greater(10, 5)
greater('apple', 'banana')
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Second exemple : `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Vérifie si la première valeur est supérieure ou égale à la seconde.
Retourne la valeur true si la première valeur est supérieure ou égale à la seconde, ou la valeur false si la première valeur est inférieure à la seconde.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est supérieure ou égale à la seconde |
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Valeur de comparaison |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true si la première valeur est supérieure ou égale à la seconde. Retourne la valeur false si la première valeur est inférieure à la seconde. |
||||

*Exemple*

Ces exemples vérifient si la première valeur est supérieure ou égale à la seconde :

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Second exemple : `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Génère un identificateur global unique (GUID) sous la forme d’une chaîne, par exemple « c2ecc88d-88c8-4096-912c-d6f2e2b138ce » :

```
guid()
```

Vous pouvez également spécifier un format différent pour le GUID autre que le format par défaut, « D », qui correspond à 32 chiffres séparés par des traits d’union.

```
guid('<format>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | Non | Chaîne | Un seul [spécificateur de format](https://msdn.microsoft.com/library/97af8hh4) pour le GUID retourné. Par défaut, il s’agit du format « D », mais vous pouvez utiliser « N », « D », « B », « P » ou « X ». |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*GUID-value*> | Chaîne | GUID généré de manière aléatoire |
||||

*Exemple*

Cet exemple illustre la génération du même GUID, mais sous forme de 32 chiffres séparés par des traits d’union et placés entre parenthèses :

```
guid('P')
```

Et retourne ce résultat : `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Vérifie si une expression est vraie ou fausse.
En fonction du résultat, retourne une valeur spécifiée.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | OUI | Boolean | Expression à vérifier |
| <*valueIfTrue*> | OUI | Quelconque | Valeur à retourner lorsque l’expression est vraie |
| <*valueIfFalse*> | OUI | Quelconque | Valeur à retourner lorsque l’expression est fausse |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Quelconque | Valeur spécifiée retournée selon que l’expression est vraie ou fausse |
||||

*Exemple*

Cet exemple retourne `"yes"`, car l’expression spécifiée retourne la valeur true.
Dans le cas contraire, l’exemple retourne `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retourne la position ou la valeur d’index de départ d’une sous-chaîne.
Cette fonction ne respecte pas la casse, et les index commencent par 0.

```
indexOf('<text>', '<searchText>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à rechercher |
| <*searchText*> | OUI | Chaîne | Sous-chaîne à rechercher |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Position ou valeur d’index de départ de la sous-chaîne spécifiée. <p>Si la chaîne est introuvable, retourne le chiffre -1. |
||||

*Exemple*

Cet exemple recherche la valeur d’index de départ de la sous-chaîne « world » dans la chaîne « hello world » :

```
indexOf('hello world', 'world')
```

Et retourne ce résultat : `6`

<a name="int"></a>

### <a name="int"></a>int

Retourne la version de type entier d’une chaîne.

```
int('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Version de type entier de la chaîne spécifiée |
||||

*Exemple*

Cet exemple illustre la création d’une version de type entier pour la chaîne « 10 » :

```
int('10')
```

Et retourne ce résultat : `10`

<a name="item"></a>

### <a name="item"></a>item

Lorsqu’elle est utilisée dans une action répétée d’un tableau, retourne l’élément actuel du tableau au cours de l’itération actuelle de l’action.
Vous pouvez également obtenir les valeurs à partir des propriétés de cet élément.

```
item()
```

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Quelconque | Élément actuel dans le tableau correspondant à l’itération actuelle de l’action |
||||

*Exemple*

Cet exemple illustre l’obtention de l’élément `body` à partir du message actuel pour l’action « Send_an_email » au sein de l’itération actuelle d’une boucle for-each :

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Retourne l’élément actuel à partir de chaque cycle d’une boucle for-each.
Utilisez cette fonction à l’intérieur de la boucle for-each.

```
items('<loopName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | OUI | Chaîne | Nom de la boucle for-each |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*item*> | Quelconque | Élément à partir du cycle en cours de la boucle for-each spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de l’élément actuel à partir de la boucle for-each spécifiée :

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Retourne la valeur d’index de l’itération actuelle à l’intérieur d’une boucle Until. Vous pouvez utiliser cette fonction à l’intérieur de boucles Until imbriquées. 

```
iterationIndexes('<loopName>')
```

| Paramètre | Obligatoire | Type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | OUI | Chaîne | Le nom de la boucle Until | 
||||| 

| Valeur de retour | Type | Description | 
| ------------ | ---- | ----------- | 
| <*index*> | Integer | La valeur d’index de l’itération actuelle à l’intérieur d’une boucle Until spécifique | 
|||| 

*Exemple* 

Cet exemple crée une variable de compteur et incrémente cette variable par un au cours de chaque itération dans une boucle Until jusqu’à ce que la valeur du compteur atteigne 5. L’exemple crée également une variable qui assure le suivi de l’index actuel pour chaque itération. Lors de chaque itération, l’exemple incrémente le compteur dans la boucle Until, assigne la valeur du compteur à la valeur d’index actuelle, puis incrémente le compteur. Vous pouvez déterminer à tout moment le numéro d’itération actuel en récupérant la valeur d’index actuelle.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Retourne la valeur ou l’objet de type JavaScript Object Notation (JSON) d’une chaîne ou d’un élément XML.

```
json('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne ou élément XML | Chaîne ou élément XML à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Objet ou type natif JSON | Objet ou valeur de type natif JSON correspondant à la chaîne ou à l’élément XML spécifiés. Si la chaîne est Null, la fonction retourne un objet vide. |
||||

*Exemple 1*

Cet exemple illustre la conversion de cette chaîne en valeur JSON :

```
json('[1, 2, 3]')
```

Et retourne ce résultat : `[1, 2, 3]`

*Exemple 2*

Cet exemple illustre la conversion de cette chaîne au format JSON :

```
json('{"fullName": "Sophia Owen"}')
```

Et retourne ce résultat :

```
{
  "fullName": "Sophia Owen"
}
```

*Exemple 3*

Cet exemple illustre la conversion de cet élément XML au format JSON :

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Et retourne ce résultat :

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersection

Retourne une collection qui contient *uniquement* les éléments communs aux collections spécifiées.
Pour qu’il apparaisse dans le résultat, un élément doit apparaître dans toutes les collections transmises à cette fonction.
Si un ou plusieurs éléments portent le même nom, le dernier élément de ce nom apparaît dans le résultat.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>... | OUI | Tableau ou objet, mais pas les deux | Collections à partir desquelles vous souhaitez *uniquement* les éléments communs |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*common-items*> | Tableau ou objet, respectivement | Collection qui contient uniquement les éléments communs aux collections spécifiées |
||||

*Exemple*

Cet exemple recherche les éléments communs dans ces tableaux :

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Et retourne un tableau comportant *uniquement* ces éléments : `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retourne une chaîne qui contient tous les éléments d’un tableau, et dont tous les caractères sont séparés par un *séparateur*.

```
join([<collection>], '<delimiter>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Array | Tableau qui comporte les éléments à joindre |
| <*delimiter*> | OUI | Chaîne | Séparateur affiché entre chaque caractère de la chaîne obtenue |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Chaîne | Chaîne obtenue créée à partir de tous les éléments du tableau spécifié |
||||

*Exemple*

Cet exemple illustre la création d’une chaîne à partir de tous les éléments de ce tableau avec le caractère spécifié comme séparateur :

```
join(createArray('a', 'b', 'c'), '.')
```

Et retourne ce résultat : `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Retourne le dernier élément d’une collection.

```
last('<collection>')
last([<collection>])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne ou tableau | Collection dans laquelle rechercher le dernier élément |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Chaîne ou tableau, respectivement | Dernier élément de la collection |
||||

*Exemple*

Ces exemples illustrent la recherche du dernier élément dans ces collections :

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Et retournent les résultats suivants :

* Premier exemple : `"d"`
* Second exemple : `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retourne la position de départ ou la valeur d’index de la dernière occurrence d’une sous-chaîne.
Cette fonction ne respecte pas la casse, et les index commencent par 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à rechercher |
| <*searchText*> | OUI | Chaîne | Sous-chaîne à rechercher |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | Retourne la position de départ ou la valeur d’index de la dernière occurrence de la sous-chaîne spécifiée. <p>Si la chaîne est introuvable, retourne le chiffre -1. |
||||

*Exemple*

Cet exemple recherche la valeur d’index de départ de la dernière occurrence de la sous-chaîne « world » dans la chaîne « hello world » :

```
lastIndexOf('hello world', 'world')
```

Et retourne ce résultat : `6`

<a name="length"></a>

### <a name="length"></a>length

Retourne le nombre d’éléments d’une collection.

```
length('<collection>')
length([<collection>])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne ou tableau | Collection contenant les éléments à comptabiliser |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Nombre d’éléments de la collection |
||||

*Exemple*

Ces exemples illustrent le comptage du nombre d’éléments contenus dans ces collections :

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Et retourne ce résultat : `4`

<a name="less"></a>

### <a name="less"></a>less

Vérifie si la première valeur est inférieure à la seconde.
Retourne la valeur true si la première valeur est inférieure à la seconde, ou la valeur false si la première valeur est supérieure à la seconde.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est inférieure à la seconde |
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Élément de comparaison |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true si la première valeur est inférieure à la seconde. Retourne la valeur false si la première valeur est égale ou supérieure à la seconde. |
||||

*Exemple*

Ces exemples vérifient si la première valeur est inférieure à la seconde.

```
less(5, 10)
less('banana', 'apple')
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Second exemple : `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Vérifie si la première valeur est inférieure ou égale à la seconde.
Retourne la valeur true si la première valeur est inférieure ou égale à la seconde, ou la valeur false si la première valeur est supérieure à la seconde.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est inférieure ou égale à la seconde |
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Élément de comparaison |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retourne la valeur true si la première valeur est inférieure ou égale à la seconde. Retourne la valeur false si la première valeur est supérieure à la seconde. |
||||

*Exemple*

Ces exemples vérifient si la première valeur est inférieure ou égale à la seconde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Et retournent les résultats suivants :

* Premier exemple : `true`
* Second exemple : `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Renvoyer l’« URL de rappel » qui appelle un déclencheur ou une action.
Cette fonction fonctionne uniquement avec les déclencheurs et actions pour les types de connecteur **HttpWebhook** et **ApiConnectionWebhook**, mais pas les types **Manual**, **Recurrence**, **HTTP** ni **APIConnection**.

```
listCallbackUrl()
```

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Chaîne | URL de rappel d’un déclencheur ou d’une action |
||||

*Exemple*

Cet exemple illustre un exemple d’URL de rappel que cette fonction peut retourner :

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Retourne la valeur la plus élevée d’une liste ou d’un tableau de nombres incluse aux deux extrémités.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>... | OUI | Entier, flottant ou les deux | Ensemble de nombres dont vous voulez la valeur la plus élevée |
| [<*number1*>, <*number2*>...] | OUI | Tableau : entier, flottant ou les deux | Tableau de nombres dont vous voulez la valeur la plus élevée |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*max-value*> | Entier ou flottant | Valeur la plus élevée dans le tableau ou l’ensemble de nombres spécifié |
||||

*Exemple*

Ces exemples illustrent l’obtention de la valeur la plus élevée de l’ensemble des nombres et du tableau :

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Et retourne ce résultat : `3`

<a name="min"></a>

### <a name="min"></a>min

Retourne la plus petite valeur d’un ensemble de nombres ou d’un tableau.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>... | OUI | Entier, flottant ou les deux | Ensemble de nombres dont vous voulez la valeur la plus petite |
| [<*number1*>, <*number2*>...] | OUI | Tableau : entier, flottant ou les deux | Tableau de nombres dont vous voulez la valeur la plus petite |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*min-value*> | Entier ou flottant | Valeur la plus petite dans le tableau ou l’ensemble de nombres spécifiés |
||||

*Exemple*

Ces exemples illustrent l’obtention de la valeur la plus petite de l’ensemble des nombres et du tableau :

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Et retourne ce résultat : `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Retourne le reste de la division de deux nombres.
Pour obtenir le résultat sous forme d’un entier, consultez [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | OUI | Entier ou flottant | Nombre à diviser par le *diviseur* |
| <*divisor*> | OUI | Entier ou flottant | Nombre qui divise le *dividende*, mais qui ne peut pas être égal à 0. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Entier ou flottant | Reste de la division du premier nombre par le second nombre |
||||

*Exemple*

Cet exemple illustre la division du premier nombre par le second nombre :

```
mod(3, 2)
```

Et retourne ce résultat : `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retourne le produit de la multiplication de deux nombres.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | OUI | Entier ou flottant | Nombre à multiplier par *multiplicand2* |
| <*multiplicand2*> | OUI | Entier ou flottant | Nombre qui multiplie *multiplicand1* |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*product-result*> | Entier ou flottant | Produit de la multiplication du premier nombre par le second nombre |
||||

*Exemple*

Ces exemples illustrent la multiplication du premier nombre par le second nombre :

```
mul(1, 2)
mul(1.5, 2)
```

Et retournent les résultats suivants :

* Premier exemple : `2`
* Second exemple : `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Renvoyer le corps correspondant à une partie spécifique de la sortie d’une action qui comporte plusieurs parties.

```
multipartBody('<actionName>', <index>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de l’action dont la sortie compte plusieurs parties |
| <*index*> | OUI | Integer | Valeur d’index pour la partie que vous souhaitez |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*body*> | Chaîne | Corps de la partie spécifiée |
||||

<a name="not"></a>

### <a name="not"></a>not

Vérifie si une expression est fausse.
Retourne la valeur true lorsque l’expression est fausse, ou la valeur false lorsque l’expression est vraie.

```json
not(<expression>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | OUI | Boolean | Expression à vérifier |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true lorsque l’expression est fausse. Retourne la valeur false lorsque l’expression est vraie. |
||||

*Exemple 1*

Ces exemples vérifient si les expressions spécifiées sont fausses :

```json
not(false)
not(true)
```

Et retournent les résultats suivants :

* Premier exemple : comme l’expression est fausse, la fonction retourne `true`.
* Deuxième exemple : comme l’expression est vraie, la fonction retourne `false`.

*Exemple 2*

Ces exemples vérifient si les expressions spécifiées sont fausses :

```json
not(equals(1, 2))
not(equals(1, 1))
```

Et retournent les résultats suivants :

* Premier exemple : comme l’expression est fausse, la fonction retourne `true`.
* Deuxième exemple : comme l’expression est vraie, la fonction retourne `false`.

<a name="or"></a>

### <a name="or"></a>or

Vérifie si au moins une expression est vraie.
Retourne la valeur true si au moins une expression est vraie ou la valeur false si toutes les expressions sont fausses.

```
or(<expression1>, <expression2>, ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>... | OUI | Boolean | Expressions à vérifier |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retourne la valeur true si au moins une expression est vraie. Retourne la valeur false lorsque toutes les expressions sont fausses. |
||||

*Exemple 1*

Ces exemples vérifient si au moins une expression est vraie :

```json
or(true, false)
or(false, false)
```

Et retournent les résultats suivants :

* Premier exemple : comme au moins une expression est vraie, la fonction retourne `true`.
* Deuxième exemple : comme les deux expressions sont fausses, la fonction retourne `false`.

*Exemple 2*

Ces exemples vérifient si au moins une expression est vraie :

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Et retournent les résultats suivants :

* Premier exemple : comme au moins une expression est vraie, la fonction retourne `true`.
* Deuxième exemple : comme les deux expressions sont fausses, la fonction retourne `false`.

<a name="outputs"></a>

### <a name="outputs"></a>outputs

Retourne les sorties d’une action lors de l’exécution. Utilisez cette fonction plutôt que `actionOutputs()`, qui correspond à `outputs()` dans le concepteur d’application logique. Bien que les deux fonctions agissent de manière identique, la fonction `outputs()` est préférée.

```
outputs('<actionName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | OUI | Chaîne | Nom de la sortie de l’action souhaitée |
|||||

| Valeur de retour | Type | Description |
| ------------ | -----| ----------- |
| <*output*> | Chaîne | Sortie de l’action spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention de la sortie de l’action Twitter `Get user` :

```
outputs('Get_user')
```

Et retourne ce résultat :

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Retourne la valeur d’un paramètre décrit dans la définition de votre flux de travail.

```
parameters('<parameterName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | OUI | Chaîne | Nom du paramètre dont vous voulez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Quelconque | Valeur du paramètre spécifié |
||||

*Exemple*

Supposez que la valeur JSON soit la suivante :

```json
{
  "fullName": "Sophia Owen"
}
```

Cet exemple illustre l’obtention de la valeur du paramètre spécifié :

```
parameters('fullName')
```

Et retourne ce résultat : `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Retourne un entier aléatoire à partir d’une plage spécifiée, qui est inclus uniquement au point de départ.

```
rand(<minValue>, <maxValue>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | OUI | Integer | Entier le plus petit de la plage |
| <*maxValue*> | OUI | Integer | Entier qui suit l’entier le plus élevé dans la plage que la fonction peut retourner |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Entier aléatoire retourné à partir de la plage spécifiée |
||||

*Exemple*

Cet exemple illustre l’obtention d’un entier aléatoire à partir de la plage spécifiée, en excluant la valeur maximale :

```
rand(1, 5)
```

Et retourne l’un de ces nombres comme résultat : `1`, `2`, `3` ou `4`

<a name="range"></a>

### <a name="range"></a>range

Retourne un tableau d’entiers qui commence par un entier spécifique.

```
range(<startIndex>, <count>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | OUI | Integer | Une valeur entière qui constitue le premier élément du tableau |
| <*count*> | OUI | Integer | Nombre d’entiers du tableau |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | Tableau dont les entiers commencent à partir de l’index spécifié |
||||

*Exemple*

Cet exemple illustre la création d’un tableau d’entiers qui commence à partir de l’index spécifié et qui possède le nombre spécifié d’entiers :

```
range(1, 4)
```

Et retourne ce résultat : `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>remplacer

Remplace une sous-chaîne par la chaîne spécifiée et retourne la chaîne de résultat. Cette fonction respecte la casse.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à remplacer |
| <*oldText*> | OUI | Chaîne | Sous-chaîne à remplacer |
| <*newText*> | OUI | Chaîne | Chaîne de remplacement |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-text*> | Chaîne | Chaîne mise à jour après le remplacement de la sous-chaîne <p>Si la sous-chaîne est introuvable, retourne la chaîne d’origine. |
||||

*Exemple*

Cet exemple recherche l’ancienne sous-chaîne dans l’ancienne chaîne, et remplace « old » par « new » :

```
replace('the old string', 'old', 'new')
```

Et retourne ce résultat : `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Supprime une propriété dans un objet et retourne l’objet mis à jour. Si la propriété que vous essayez de supprimer n’existe pas, la fonction retourne l’objet d’origine.

```
removeProperty(<object>, '<property>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dont vous souhaitez supprimer une propriété |
| <*property*> | OUI | Chaîne | Nom de la propriété à supprimer |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Objet JSON mis à jour sans la propriété spécifiée |
||||

Pour supprimer une propriété enfant d’une propriété existante, utilisez cette syntaxe :

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dont vous voulez supprimer la propriété |
| <*parent-property*> | OUI | Chaîne | Nom de la propriété parente avec la propriété enfant que vous voulez supprimer |
| <*child-property*> | OUI | Chaîne | Nom de la propriété enfant à supprimer |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Objet JSON mis à jour dont vous avez supprimé la propriété enfant |
||||

*Exemple 1*

Cet exemple supprime la propriété `middleName` d’un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json), et retourne l’objet mis à jour :

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Voici l’objet JSON actuel :

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Voici l’objet JSON mis à jour :

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exemple 2*

Cet exemple supprime la propriété enfant `middleName` d’une propriété parente `customerName` dans un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json), et retourne l’objet mis à jour :

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Voici l’objet JSON actuel :

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Voici l’objet JSON mis à jour :

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Retourne les entrées et les sorties de toutes les actions à l’intérieur de l’action délimitée spécifiée, comme une action `For_each`, `Until` ou `Scope`. Cette fonction est utile pour retourner les résultats d’une action qui a échoué, pour vous permettre de diagnostiquer et de gérer les exceptions. Pour plus d’informations, consultez [Obtenir le contexte et les résultats des échecs](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | OUI | Chaîne | Nom de l’action délimitée de laquelle retourner les entrées et les sorties de toutes les actions internes |
||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*array-object*> | Objet tableau | Tableau qui contient des tableaux d’entrées et de sorties de chaque action qui apparaît à l’intérieur de l’action délimitée spécifiée |
||||

*Exemple*

Cet exemple retourne les entrées et les sorties de chaque itération d’une action HTTP qui est à l’intérieur d’une boucle `For_each` en utilisant la fonction `result()` dans l’action `Compose` :

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Voici comment l’exemple de tableau retourné peut se présenter, où l’objet externe `outputs` contient les entrées et les sorties de chaque itération des actions à l’intérieur de l’action `For_each`.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>SetProperty

Définit la valeur d’une propriété d’un objet JSON et retourne l’objet mis à jour. Si la propriété que vous essayez de définir n’existe pas, la propriété est ajoutée à l’objet. Pour ajouter une nouvelle propriété, utilisez la fonction [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dont vous souhaitez définir la propriété |
| <*property*> | OUI | Chaîne | Nom de la propriété existante ou nouvelle à définir |
| <*value*> | OUI | Quelconque | Valeur à définir pour la propriété spécifiée |
|||||

Pour définir la propriété enfant dans un objet enfant, utilisez à la place un appel `setProperty()` imbriqué. Sinon, la fonction retourne seulement l’objet enfant comme sortie.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | OUI | Object | Objet JSON dont vous souhaitez définir la propriété |
| <*parent-property*> | OUI | Chaîne | Nom de la propriété parente avec la propriété enfant que vous voulez définir |
| <*child-property*> | OUI | Chaîne | Nom de la propriété enfant à définir |
| <*value*> | OUI | Quelconque | Valeur à définir pour la propriété spécifiée |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Objet JSON mis à jour dont vous définissez la propriété |
||||

*Exemple 1*

Cet exemple définit la propriété `surName` dans un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json). La fonction affecte la valeur spécifiée à la propriété et retourne l’objet mis à jour :

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Voici l’objet JSON actuel :

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Voici l’objet JSON mis à jour :

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exemple 2*

Cet exemple définit la propriété enfant `surName` pour la propriété parente `customerName` dans un objet JSON, qui est converti d’une chaîne en un format JSON avec la fonction [JSON()](#json). La fonction affecte la valeur spécifiée à la propriété et retourne l’objet mis à jour :

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Voici l’objet JSON actuel :

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Voici l’objet JSON mis à jour :

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

Supprime des éléments du début d’une collection et retourne *tous les autres* éléments.

```
skip([<collection>], <count>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Array | Collection dont vous voulez supprimer des éléments |
| <*count*> | OUI | Integer | Entier positif correspondant au nombre d’éléments à supprimer au début |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | Collection mise à jour à l’issue de la suppression des éléments spécifiés |
||||

*Exemple*

Cet exemple illustre la suppression d’un élément, le nombre 0, au début du tableau spécifié :

```
skip(createArray(0, 1, 2, 3), 1)
```

Et retourne ce tableau avec les éléments restants : `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retourne un tableau qui contient des sous-chaînes, séparées par des virgules, extraites sur la base du caractère délimiteur spécifié dans la chaîne d’origine.

```
split('<text>', '<delimiter>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | La chaîne pour séparer les sous-chaînes en fonction du délimiteur spécifié dans la chaîne d’origine |
| <*delimiter*> | OUI | Chaîne | Le caractère dans la chaîne d’origine à utiliser comme délimiteur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Un tableau qui contient les sous-chaînes extraites de la chaîne d’origine, séparées par des virgules |
||||

*Exemple*

Cet exemple crée un tableau avec des sous-chaînes extraites de la chaîne spécifiée selon le caractère spécifié comme délimiteur :

```
split('a_b_c', '_')
```

Et retourne ce tableau en tant que résultat : `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retourne le début du jour d’un horodatage.

```
startOfDay('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage spécifié, mais commençant à la marque zéro-heure du jour |
||||

*Exemple*

Cet exemple recherche le début du jour pour cet horodatage :

```
startOfDay('2018-03-15T13:30:30Z')
```

Et retourne ce résultat : `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retourne le début de l’heure d’un horodatage.

```
startOfHour('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage spécifié, mais commençant à la marque zéro-minute de l’heure |
||||

*Exemple*

Cet exemple recherche le début de l’heure pour cet horodatage :

```
startOfHour('2018-03-15T13:30:30Z')
```

Et retourne ce résultat : `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retourne le début du mois pour un horodatage.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage spécifié, mais démarrant le premier jour du mois à la marque zéro-heure |
||||

*Exemple*

Cet exemple retourne le début du mois pour cet horodatage :

```
startOfMonth('2018-03-15T13:30:30Z')
```

Et retourne ce résultat : `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Vérifie si une chaîne commence par une sous-chaîne spécifique.
Retourne la valeur true lorsque la sous-chaîne est trouvée ou la valeur false lorsqu’elle est introuvable.
Cette fonction ne respecte pas la casse.

```
startsWith('<text>', '<searchText>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne à vérifier |
| <*searchText*> | OUI | Chaîne | Chaîne de départ à rechercher |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retourne la valeur true lorsque la sous-chaîne de début est trouvée. Retourne la valeur false lorsqu’elle est introuvable. |
||||

*Exemple 1*

Cet exemple vérifie si la chaîne « hello world » commence par la sous-chaîne « hello » :

```
startsWith('hello world', 'hello')
```

Et retourne ce résultat : `true`

*Exemple 2*

Cet exemple vérifie si la chaîne « hello world » commence par la sous-chaîne « greetings » :

```
startsWith('hello world', 'greetings')
```

Et retourne ce résultat : `false`

<a name="string"></a>

### <a name="string"></a>string

Retourne la version de type chaîne d’une valeur.

```
string(<value>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Quelconque | Valeur à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*string-value*> | Chaîne | Version de type chaîne de la valeur spécifiée |
||||

*Exemple 1*

Cet exemple illustre la création de la version de type chaîne pour ce nombre :

```
string(10)
```

Et retourne ce résultat : `"10"`

*Exemple 2*

Cet exemple illustre la création d’une chaîne pour l’objet JSON spécifié et utilise le caractère de barre oblique inverse (\\) comme caractère d’échappement pour le guillemet double (").

```
string( { "name": "Sophie Owen" } )
```

Et retourne ce résultat : `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retourne le résultat de la soustraction du second nombre du premier.

```
sub(<minuend>, <subtrahend>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | OUI | Entier ou flottant | Nombre à partir duquel soustraire le *plus petit terme* |
| <*subtrahend*> | OUI | Entier ou flottant | Nombre à soustraire du *plus petit terme* |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*result*> | Entier ou flottant | Résultat de la soustraction du second nombre du premier |
||||

*Exemple*

Cet exemple illustre la soustraction du second nombre du premier :

```
sub(10.3, .3)
```

Et retourne ce résultat : `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Retourne les caractères d’une chaîne, en commençant à partir de la position spécifiée ou de l’index.
Les valeurs d’index commencent par le chiffre 0.

```
substring('<text>', <startIndex>, <length>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne dont vous souhaitez les caractères |
| <*startIndex*> | OUI | Integer | Une valeur positive supérieure ou égale à 0 que vous souhaitez utiliser comme la valeur de position ou l’index de départ |
| <*length*> | OUI | Integer | Nombre positif de caractères que vous souhaitez dans la sous-chaîne |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*substring-result*> | Chaîne | Sous-chaîne contenant le nombre spécifié de caractères, en commençant à la position d’index spécifiée dans la chaîne source |
||||

*Exemple*

Cet exemple illustre la création d’une sous-chaîne de cinq caractères à partir de la chaîne spécifiée, en commençant à partir de la valeur d’index 6 :

```
substring('hello world', 6, 5)
```

Et retourne ce résultat : `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Soustrait un nombre d’unités de temps d’un horodatage.
Voir aussi [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage |
| <*interval*> | OUI | Integer | Nombre d’unités de temps spécifiées à soustraire |
| <*timeUnit*> | OUI | Chaîne | L’unité de temps à utiliser avec *interval* : "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Chaîne | Horodatage moins le nombre d’unités de temps spécifié |
||||

*Exemple 1*

Cet exemple soustrait un jour de cet horodatage :

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Et retourne ce résultat : `"2018-01-01T00:00:00:0000000Z"`

*Exemple 2*

Cet exemple soustrait un jour de cet horodatage :

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Et retourne ce résultat en utilisant le format « D » facultatif : `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Retourne des éléments du début d’une collection.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | OUI | Chaîne ou tableau | Collection dont vous souhaitez des éléments |
| <*count*> | OUI | Integer | Entier positif correspondant au nombre d’éléments que vous souhaitez à partir du début |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*subset*> ou [<*subset*>] | Chaîne ou tableau, respectivement | Chaîne ou tableau qui comporte le nombre spécifié d’éléments obtenus à partir du début de la collection d’origine |
||||

*Exemple*

Ces exemples illustrent l’obtention du nombre spécifié d’éléments à partir du début de ces collections :

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Et retournent les résultats suivants :

* Premier exemple : `"abc"`
* Second exemple : `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Retourne la valeur de la propriété `ticks` pour un horodatage spécifique.
Une *graduation* est un intervalle de 100 nanosecondes.

```
ticks('<timestamp>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | OUI | Chaîne | Chaîne d’un horodatage |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Nombre de graduations depuis l’horodatage spécifié |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retourne une chaîne en minuscules. Si un caractère de la chaîne n’a pas de version en minuscules, ce caractère reste tel quel dans la chaîne retournée.

```
toLower('<text>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne à retourner en minuscules |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | Chaîne | Chaîne d’origine en minuscules |
||||

*Exemple*

Cet exemple illustre la conversion de cette chaîne en minuscules :

```
toLower('Hello World')
```

Et retourne ce résultat : `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retourne une chaîne en majuscules. Si un caractère de la chaîne n’a pas de version en majuscules, ce caractère reste tel quel dans la chaîne retournée.

```
toUpper('<text>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne à retourner en majuscules |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | Chaîne | Chaîne d’origine en majuscules |
||||

*Exemple*

Cet exemple illustre la conversion de cette chaîne en majuscules :

```
toUpper('Hello World')
```

Et retourne ce résultat : `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>trigger

Retourne la sortie d’un déclencheur lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression.

* Dans les entrées d’un déclencheur, cette fonction retourne la sortie de l’exécution précédente.

* Dans une condition d’un déclencheur, cette fonction retourne la sortie de l’exécution actuelle.

Par défaut, la fonction fait référence à l’objet de déclencheur dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur.
En outre, des versions raccourcies sont disponibles pour cette fonction. Voir [triggerOutputs()](#triggerOutputs) et [triggerBody()](#triggerBody).

```
trigger()
```

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Chaîne | Sortie d’un déclencheur à l’exécution |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Renvoyer la sortie `body` d’un déclencheur lors de l’exécution.
Raccourci de `trigger().outputs.body`.
Voir [trigger()](#trigger).

```
triggerBody()
```

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Chaîne | Sortie `body` du déclencheur |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retourne un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur.

```
triggerFormDataMultiValues('<key>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Tableau contenant toutes les valeurs qui correspondent à la clé spécifiée |
||||

*Exemple*

Cet exemple illustre la création d’un tableau à partir de la valeur de la clé « feedUrl » dans la sortie form-data ou form-encoded d’un déclencheur RSS :

```
triggerFormDataMultiValues('feedUrl')
```

Et retourne ce tableau en tant qu’exemple de résultat : `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retourne une chaîne contenant une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur.
Si la fonction trouve plusieurs correspondances, elle génère une erreur.

```
triggerFormDataValue('<key>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*key-value*> | Chaîne | Valeur de la clé spécifiée |
||||

*Exemple*

Cet exemple illustre la création d’une chaîne à partir de la valeur de la clé « feedUrl » dans la sortie form-data ou form-encoded d’un déclencheur RSS :

```
triggerFormDataValue('feedUrl')
```

Et retourne cette chaîne en tant qu’exemple de résultat : `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Retourne le corps correspondant à une partie spécifique dans la sortie d’un déclencheur qui comporte plusieurs parties.

```
triggerMultipartBody(<index>)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*index*> | OUI | Integer | Valeur d’index pour la partie que vous souhaitez |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*body*> | Chaîne | Corps de la partie spécifiée dans la sortie en plusieurs parties d’un déclencheur |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Renvoyer la sortie d’un déclencheur lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON.
Raccourci de `trigger().outputs`.
Voir [trigger()](#trigger).

```
triggerOutputs()
```

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Chaîne | Sortie d’un déclencheur à l’exécution  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

Supprime les espaces blancs de début et de fin d’une chaîne et retourne la chaîne mise à jour.

```
trim('<text>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | OUI | Chaîne | Chaîne contenant les espaces blancs de début et de fin à supprimer |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedText*> | Chaîne | Version mise à jour de la chaîne d’origine sans espace blanc de début ou de fin |
||||

*Exemple*

Cet exemple illustre la suppression des espaces blancs de début et de fin de la chaîne « Hello World » :

```
trim(' Hello World  ')
```

Et retourne ce résultat : `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Retourne une collection qui contient *tous* les éléments des collections spécifiées.
Pour qu’il apparaisse dans le résultat, un élément peut apparaître dans n’importe quelle collection transmise à cette fonction. Si un ou plusieurs éléments portent le même nom, le dernier élément de ce nom apparaît dans le résultat.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>...  | OUI | Tableau ou objet, mais pas les deux | Collections dont vous souhaitez *tous* les éléments |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Tableau ou objet, respectivement | Collection qui contient tous les éléments des collections spécifiées (pas de doublons) |
||||

*Exemple*

Cet exemple illustre l’obtention de *tous* les éléments de ces collections :

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Et retourne ce résultat : `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retourne une version encodée sous forme d’URI d’une chaîne en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement.
Utilisez cette fonction plutôt que la fonction [encodeUriComponent()](#encodeUriComponent).
Bien que les deux fonctions agissent de manière identique, la fonction `uriComponent()` est préférée.

```
uriComponent('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne à convertir au format encodé sous forme d’URI |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Chaîne | Chaîne encodée sous forme d’URI avec des caractères d’échappement |
||||

*Exemple*

Cet exemple illustre la création d’une version encodée sous forme d’URI pour cette chaîne :

```
uriComponent('https://contoso.com')
```

Et retourne ce résultat : `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourne la version binaire d’un composant d’URI.

```
uriComponentToBinary('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne encodée sous forme d’URI à convertir |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Chaîne | Version binaire de la chaîne encodée sous forme d’URI. Le contenu binaire est encodé en Base64 et représenté par `$content`. |
||||

*Exemple*

Cet exemple illustre la création de la version binaire de cette chaîne encodée sous forme d’URI :

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Et retourne ce résultat :

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retourne la version de type chaîne d’une chaîne encodée sous forme d’URI, en décodant efficacement la chaîne encodée sous forme d’URI.

```
uriComponentToString('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne encodée sous forme d’URI à décoder |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Chaîne | Version décodée de la chaîne encodée sous forme d’URI |
||||

*Exemple*

Cet exemple illustre la création de la version de type chaîne décodée pour cette chaîne encodée sous forme d’URI :

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Et retourne ce résultat : `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Renvoyer la valeur `host` pour un URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `host` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*host-value*> | Chaîne | Valeur `host` de l’URI spécifié |
||||

*Exemple*

Cet exemple recherche la valeur `host` pour cet URI :

```
uriHost('https://www.localhost.com:8080')
```

Et retourne ce résultat : `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Renvoyer la valeur `path` pour un URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `path` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*path-value*> | Chaîne | Valeur `path` de l’URI spécifié. Si `path` n’a pas de valeur, retourne le caractère « / ». |
||||

*Exemple*

Cet exemple recherche la valeur `path` pour cet URI :

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Et retourne ce résultat : `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Renvoyer les valeurs `path` et `query` pour un URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez les valeurs `path` et `query` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Chaîne | Valeur `path` et `query` de l’URI spécifié. Si `path` ne spécifie pas de valeur, retourne le caractère « / ». |
||||

*Exemple*

Cet exemple recherche les valeurs `path` et `query` pour cet URI :

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Et retourne ce résultat : `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Renvoyer la valeur `port` pour un URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `port` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*port-value*> | Integer | Valeur `port` de l’URI spécifié. Si `port` ne spécifie pas de valeur, retourne le port par défaut du protocole. |
||||

*Exemple*

Cet exemple retourne la valeur `port` pour cet URI :

```
uriPort('http://www.localhost:8080')
```

Et retourne ce résultat : `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Renvoyer la valeur `query` pour un URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `query` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*query-value*> | Chaîne | Valeur `query` de l’URI spécifié |
||||

*Exemple*

Cet exemple retourne la valeur `query` pour cet URI :

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Et retourne ce résultat : `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Renvoyer la valeur `scheme` pour un URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `scheme` |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*scheme-value*> | Chaîne | Valeur `scheme` de l’URI spécifié |
||||

*Exemple*

Cet exemple retourne la valeur `scheme` pour cet URI :

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Et retourne ce résultat : `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retourne l’horodatage actuel.

```
utcNow('<format>')
```

Si vous le souhaitez, vous pouvez spécifier un autre format avec le paramètre <*format*>.


| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | Non | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | Chaîne | Date et heure actuelles |
||||

*Exemple 1*

Supposons que nous soyons le 15 avril 2018 à 13:00:00.
Cet exemple illustre l’obtention de l’horodatage actuel :

```
utcNow()
```

Et retourne ce résultat : `"2018-04-15T13:00:00.0000000Z"`

*Exemple 2*

Supposons que nous soyons le 15 avril 2018 à 13:00:00.
Cet exemple illustre l’obtention de l’horodatage actuel en utilisant le format « D » facultatif :

```
utcNow('D')
```

Et retourne ce résultat : `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variables

Renvoyer la valeur d’une variable spécifiée.

```
variables('<variableName>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | OUI | Chaîne | Nom de la variable dont vous souhaitez la valeur |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*variable-value*> | Quelconque | Valeur de la variable spécifiée |
||||

*Exemple*

Supposons que la valeur actuelle de la variable « numItems » corresponde à 20.
Cet exemple illustre l’obtention de la valeur entière de cette variable :

```
variables('numItems')
```

Et retourne ce résultat : `20`

<a name="workflow"></a>

### <a name="workflow"></a>flux de travail

Renvoyer tous les détails sur le flux de travail proprement dit pendant l’exécution.

```
workflow().<property>
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*property*> | Non | Chaîne | Nom de la propriété de flux de travail dont vous souhaitez la valeur <p>Un objet de flux de travail possède ces propriétés : **name**, **type**, **id**, **location** et **run**. La valeur de la propriété **run** est également un objet qui possède ces propriétés : **name**, **type** et **id**. |
|||||

*Exemple*

Cet exemple retourne le nom de l’exécution actuelle d’un flux de travail :

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>Xml

Retourne la version XML d’une chaîne qui contient un objet JSON.

```
xml('<value>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | OUI | Chaîne | Chaîne contenant l’objet JSON à convertir <p>L’objet JSON ne doit avoir qu’une seule propriété racine, qui ne peut pas être un tableau. <br>Utilisez le caractère de barre oblique inverse (\\) comme caractère d’échappement pour le guillemet double ("). |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | Élément XML encodé correspondant à la chaîne ou à l’objet JSON spécifié |
||||

*Exemple 1*

Cet exemple illustre la création de la version XML de cette chaîne, qui contient un objet JSON :

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Et retourne le résultat XML suivant :

```xml
<name>Sophia Owen</name>
```

*Exemple 2*

Supposons que vous disposiez de cet objet JSON :

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Cet exemple illustre la création d’un élément XML pour une chaîne qui contient l’objet JSON :

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Et retourne le résultat XML suivant :

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Vérifie si le code XML contient des valeurs ou des nœuds qui correspondent à une expression de langage XPath et retourne les valeurs ou les nœuds correspondants. Une expression XPath, ou simplement « XPath », vous permet de parcourir une structure de document XML afin de pouvoir sélectionner des nœuds ou des valeurs de calcul dans le contenu XML.

```
xpath('<xml>', '<xpath>')
```

| Paramètre | Obligatoire | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*xml*> | OUI | Quelconque | Chaîne XML pour rechercher des nœuds ou des valeurs qui correspondent à une valeur d’expression XPath |
| <*xpath*> | OUI | Quelconque | Expression XPath utilisée pour rechercher des nœuds ou des valeurs XML correspondants |
|||||

| Valeur de retour | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Nœud XML lorsqu’un seul nœud correspond à l’expression XPath spécifiée |
| <*value*> | Quelconque | Valeur d’un nœud XML lorsqu’un seul nœud correspond à l’expression XPath spécifiée |
| [<*xml-node1*>, <*xml-node2*>...] </br>-ou- </br>[<*value1*>, <*value2*>...] | Array | Tableau contenant des nœuds ou valeurs XML qui correspondent à l’expression XPath spécifiée |
||||

*Exemple 1*

Cet exemple recherche les nœuds qui correspondent au nœud `<name></name>` dans les arguments spécifiés, et retourne un tableau contenant ces valeurs de nœud :

`xpath(xml(parameters('items')), '/produce/item/name')`

Voici les arguments :

* Chaîne « items », qui contient ce code XML :

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  L’exemple utilise la fonction [parameters()](#parameters) pour obtenir la chaîne XML à partir de l’argument « items », mais il doit également convertir la chaîne au format XML à l’aide de la fonction [xml()](#xml).

* Cette expression XPath, qui est transmise en tant que chaîne :

  `"/produce/item/name"`

Voici le tableau de résultats avec les nœuds qui correspondent à `<name></name` :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemple 2*

À la suite de l’exemple 1, cet exemple recherche les nœuds qui correspondent au nœud `<count></count>` et ajoute ces valeurs de nœud avec la fonction `sum()` :

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Et retourne ce résultat : `30`

*Exemple 3*

Pour cet exemple, les deux expressions recherchent des nœuds qui correspondent au nœud `<location></location>`, dans les arguments spécifiés, notamment XML avec un espace de noms. Les expressions utilisent le caractère de barre oblique inverse (\\) comme caractère d’échappement pour le guillemet double (").

* *Expression 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expression 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Voici les arguments :

* Ce code XML, qui inclut l’espace de noms du document XML, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* L’une ou l’autre des expressions XPath ici :

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Voici le nœud obtenu qui correspond au nœud `<location></location>` :

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemple 4*

À la suite de l’exemple 3, cet exemple recherche la valeur du nœud `<location></location>` :

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Et retourne ce résultat : `"Paris"`

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [langage de définition du flux de travail](../logic-apps/logic-apps-workflow-definition-language.md)
