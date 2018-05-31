---
title: Fonctions du langage de définition du flux de travail - Azure Logic Apps | Microsoft Docs
description: En savoir plus sur les fonctions que vous pouvez utiliser dans les définitions du flux de travail des applications logiques
services: logic-apps
author: ecfan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 0155e35641a0407fe48c4da07400fa188152b0af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182249"
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Informations de référence des fonctions du langage de définition du flux de travail pour Azure Logic Apps

Cet article décrit les fonctions que vous pouvez utiliser lorsque vous créez des flux de travail avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Pour en savoir plus sur les définitions d’application logique, consultez [Schéma du langage de définition du flux de travail pour Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Dans la syntaxe des définitions de paramètres, un point d’interrogation (?) affiché après un paramètre signifie que ce paramètre est facultatif. Par exemple, consultez [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>action

Retourne la sortie de l’action *en cours* lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression. Par défaut, cette fonction fait référence à l’objet d’action dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur. Voir aussi [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Vous pouvez utiliser la fonction `action()` uniquement dans les emplacements suivants : 

* La propriété `unsubscribe` pour une action de Webhook afin de pouvoir accéder au résultat de la demande `subscribe` d’origine
* La propriété `trackedProperties` d’une action
* La condition de boucle `do-until` d’une action

```
action()
action().outputs.body.<property> 
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | Non  | Chaîne | Nom de propriété de l’objet d’action dont vous souhaitez la valeur : **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** et **clientTrackingId**. Vous trouverez ces propriétés dans le portail Azure en passant en revue les détails d’un historique des exécutions spécifique. Pour plus d’informations, consultez [API REST - Actions d’exécution du workflow](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | -----| ----------- | 
| <*action-output*> | Chaîne | Sortie de la propriété ou de l’action en cours | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Retourne la sortie `body` d’une action lors de l’exécution. Raccourci de `actions('<actionName>').outputs.body`. Voir [body()](#body) et [actions()](#actions).

```
actionBody('<actionName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Nom de la sortie `body` de l’action souhaitée | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="actionoutputs"></a>actionOutputs

Retourne la sortie d’une action lors de l’exécution. Raccourci de `actions('<actionName>').outputs`. Voir [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Nom de la sortie de l’action souhaitée | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="actions"></a>actions

Retourne la sortie d’une action lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression. Par défaut, la fonction fait référence à l’objet d’action dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur. Pour les versions raccourcies, consultez [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) et [body()](#body). Pour l’action actuelle, consultez [action()](#action).

> [!NOTE] 
> Auparavant, vous pouviez utiliser la fonction `actions()` ou l’élément `conditions` pour spécifier qu’une action était exécutée en fonction de la sortie d’une autre action. Toutefois, pour déclarer explicitement des dépendances entre des actions, vous devez maintenant utiliser la propriété `runAfter` de l’action dépendante. Pour en savoir plus sur la propriété `runAfter`, consultez [Intercepter et gérer les échecs avec la propriété runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Nom de l’objet d’action dont vous souhaitez la sortie  | 
| <*property*> | Non  | Chaîne | Nom de propriété de l’objet d’action dont vous souhaitez la valeur : **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** et **clientTrackingId**. Vous trouverez ces propriétés dans le portail Azure en passant en revue les détails d’un historique des exécutions spécifique. Pour plus d’informations, consultez [API REST - Actions d’exécution du workflow](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="add"></a>add

Retourne le résultat de l’addition de deux nombres.

```
add(<summand_1>, <summand_2>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | OUI | Entier, flottant ou mixte | Nombres à ajouter | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="adddays"></a>addDays

Ajoute un nombre de jours à un horodatage.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*days*> | OUI | Entier  | Nombre de jours positif ou négatif à ajouter | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="addhours"></a>addHours

Ajoute un nombre d’heures à un horodatage.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*hours*> | OUI | Entier  | Nombre d’heures positif ou négatif à ajouter | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="addminutes"></a>addMinutes

Ajoute un nombre de minutes à un horodatage.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*minutes*> | OUI | Entier  | Nombre de minutes positif ou négatif à ajouter | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="addproperty"></a>addProperty

Ajoute une propriété et sa valeur, ou une paire nom-valeur, à un objet JSON et retourne l’objet mis à jour. Si l’objet existe déjà lors de l’exécution, la fonction génère une erreur.

```
addProperty(<object>, '<property>', <value>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | OUI | Object | Objet JSON dans lequel vous souhaitez ajouter une propriété | 
| <*property*> | OUI | Chaîne | Nom de la propriété à ajouter | 
| <*value*> | OUI | Quelconque | Valeur de la propriété |
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | Objet JSON mis à jour avec la propriété spécifiée | 
|||| 

*Exemple*

Cet exemple illustre l’ajoute de la propriété `accountNumber` à l’objet `customerProfile`, qui est converti au format JSON avec la fonction [JSON()](#json). La fonction attribue une valeur qui est générée par la fonction [guid()](#guid), et retourne l’objet mis à jour :

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>addSeconds

Ajoute un nombre de secondes à un horodatage.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*seconds*> | OUI | Entier  | Nombre de secondes positif ou négatif à ajouter | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="addtotime"></a>addToTime

Ajoute un nombre d’unités de temps à un horodatage. Voir aussi [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*interval*> | OUI | Entier  | Nombre d’unités de temps spécifiées à ajouter | 
| <*timeUnit*> | OUI | Chaîne | Unité de temps à utiliser avec le paramètre *interval* : « Second », « Minute », « Hour », « Day », « Week », « Month », « Year » | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | Chaîne | Horodatage plus nombre d’unités de temps spécifié  | 
|||| 

*Exemple 1*

Cet exemple illustre l’ajout d’un jour à l’horodatage spécifié :

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

Et retourne ce résultat : `"2018-01-02T00:00:00:0000000Z"`

*Exemple 2*

Cet exemple illustre l’ajout d’un jour à l’horodatage spécifié :

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Et retourne le résultat en utilisant le format « D » facultatif : `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>and

Vérifie si toutes les expressions sont vraies. Retourne la valeur true lorsque toutes les expressions sont vraies ou la valeur false lorsque au moins une expression est fausse.

```
and(<expression1>, <expression2>, ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>... | OUI | Booléen | Expressions à vérifier | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | -----| ----------- | 
| true ou false | Booléen | Retourne la valeur true si toutes les expressions sont vraies. Retourne la valeur false si au moins une expression est fausse. | 
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
* Deuxième exemple : comme une expression est fausse, la valeur `false` est retournée.
* Troisième exemple : comme les deux expressions sont fausses, la valeur `false` est retournée.

*Exemple 2*

Ces exemples vérifient si toutes les expressions spécifiées sont vraies :

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Et retournent les résultats suivants :

* Premier exemple : comme les deux expressions sont vraies, la valeur `true` est retournée. 
* Deuxième exemple : comme une expression est fausse, la valeur `false` est retournée.
* Troisième exemple : comme les deux expressions sont fausses, la valeur `false` est retournée.

<a name="array"></a>

## <a name="array"></a>array

Retourne un tableau à partir d’une entrée spécifique unique. Pour des entrées multiples, consultez [createArray()](#createArray). 

```
array('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne permettant de créer un tableau | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*valeur*>] | Tableau | Tableau qui contient l’entrée spécifiée unique | 
|||| 

*Exemple*

Cet exemple illustre la création d’un tableau à partir de la chaîne « hello » :

```
array('hello')
```

Et retourne ce résultat : `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>base64

Retourne la version encodée en Base64 d’une chaîne.

```
base64('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne d’entrée | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="base64tobinary"></a>base64ToBinary

Retourne la version binaire d’une chaîne encodée en Base64.

```
base64ToBinary('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="base64tostring"></a>base64ToString

Retourne la version de type chaîne d’une chaîne encodée en Base64, en décodant efficacement la chaîne Base64. Utilisez cette fonction plutôt que la fonction [decodeBase64()](#decodeBase64). Bien que les deux fonctions agissent de manière identique, la fonction `base64ToString()` est préférée.

```
base64ToString('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à décoder | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="binary"></a>binaire 

Retourne la version binaire d’une chaîne.

```
binary('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="body"></a>body

Retourne la sortie `body` d’une action lors de l’exécution. Raccourci de `actions('<actionName>').outputs.body`. Voir [actionBody()](#actionBody) et [actions()](#actions).

```
body('<actionName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Nom de la sortie `body` de l’action souhaitée | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="bool"></a>bool

Retourne la version booléenne d’une valeur.

```
bool(<value>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Quelconque | Valeur à convertir | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Version booléenne de la valeur spécifiée | 
|||| 

*Exemple*

Ces exemples illustrent la conversion des valeurs spécifiées en valeurs booléennes : 

```
bool(1)
bool(0)
```

Et retournent les résultats suivants : 

* Premier exemple : `true` 
* Deuxième exemple : `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>coalesce

Retourne la première valeur autre que Null d’un ou plusieurs paramètres. Les chaînes vides, les tableaux vides et les objets vides ne sont pas null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>... | OUI | N’importe lequel, possibilité de combiner plusieurs types | Recherche de la valeur Null dans un ou plusieurs éléments | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="concat"></a>concat

Combine au moins deux chaînes et retourne la chaîne combinée. 

```
concat('<text1>', '<text2>', ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text1*>, <*text2*>... | OUI | Chaîne | Au moins deux chaînes à combiner | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Chaîne | Chaîne créée à partir des chaînes d’entrée combinées | 
|||| 

*Exemple*

Cet exemple illustre la combinaison des chaînes « Hello » et « World » :

```
concat('Hello', 'World')
```

Et retourne ce résultat : `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>contains

Vérifie si une collection contient un élément spécifique. Retourne la valeur true lorsque l’élément est trouvé ou la valeur false lorsqu’il est introuvable. Cette fonction respecte la casse.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Plus précisément, cette fonction agit sur ces types de collection : 

* Une *chaîne* pour y rechercher une *sous-chaîne*
* Un *tableau* pour y rechercher une *valeur*
* Un *ensemble de clés* pour y rechercher une *clé*

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne, tableau ou ensemble de clés | Collection à vérifier | 
| <*value*> | OUI | Respectivement, chaîne, tableau ou ensemble de clés | Élément à rechercher | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true lorsque l’élément est trouvé. Retourne la valeur false lorsque l’élément est introuvable. |
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

## <a name="convertfromutc"></a>convertFromUtc

Convertit un horodatage du temps universel coordonné (UTC) au fuseau horaire cible.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*destinationTimeZone*> | OUI | Chaîne | Nom du fuseau horaire cible. Pour plus d’informations, consultez [ID de fuseau horaire](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="converttimezone"></a>convertTimeZone

Convertit un horodatage du fuseau horaire source au fuseau horaire cible.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*sourceTimeZone*> | OUI | Chaîne | Nom du fuseau horaire source. Pour plus d’informations, consultez [ID de fuseau horaire](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | OUI | Chaîne | Nom du fuseau horaire cible. Pour plus d’informations, consultez [ID de fuseau horaire](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="converttoutc"></a>convertToUtc

Convertit un horodatage du fuseau horaire source en temps universel coordonné (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*sourceTimeZone*> | OUI | Chaîne | Nom du fuseau horaire source. Pour plus d’informations, consultez [ID de fuseau horaire](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="createarray"></a>createArray

Retourne un tableau à partir de plusieurs entrées. Pour les tableaux à entrée unique, consultez [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*>... | OUI | N’importe lequel, mais pas de combinaison | Au moins deux éléments pour créer le tableau | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*object1*>, <*object2*>...] | Tableau | Tableau créé à partir de tous les éléments d’entrée | 
|||| 

*Exemple*

Cet exemple illustre la création d’un tableau à partir de ces entrées :

```
createArray('h', 'e', 'l', 'l', 'o')
```

Et retourne ce résultat : `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

Retourne un URI de données pour une chaîne. 

```
dataUri('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="datauritobinary"></a>dataUriToBinary

Retourne la version binaire d’un URI (Uniform Resource Identifier) de données. Utilisez cette fonction plutôt que la fonction [decodeDataUri()](#decodeDataUri). Bien que les deux fonctions agissent de manière identique, la fonction `decodeDataUri()` est préférée.

```
dataUriToBinary('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | URI de données à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="datauritostring"></a>dataUriToString

Retourne la version de type chaîne d’un URI de données.

```
dataUriToString('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | URI de données à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="dayofmonth"></a>dayOfMonth

Retourne le jour du mois d’un horodatage. 

```
dayOfMonth('<timestamp>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*day-of-month*> | Entier  | Jour du mois de l’horodatage spécifié | 
|||| 

*Exemple*

Cet exemple retourne le numéro du jour du mois à partir de cet horodatage :

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>dayOfWeek

Retourne le jour de la semaine à partir d’un horodatage.  

```
dayOfWeek('<timestamp>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*day-of-week*> | Entier  | Jour de la semaine à partir de l’horodatage spécifié où dimanche représente 0, lundi représente 1, etc. | 
|||| 

*Exemple*

Cet exemple retourne le numéro du jour de la semaine à partir de cet horodatage :

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>dayOfYear

Retourne le jour de l’année à partir d’un horodatage. 

```
dayOfYear('<timestamp>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*day-of-year*> | Entier  | Jour de l’année à partir de l’horodatage spécifié | 
|||| 

*Exemple*

Cet exemple retourne le numéro du jour de l’année à partir de cet horodatage :

```
dayOfYear('2018-03-15T13:27:36Z')
```

Et retourne ce résultat : `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Retourne la version de type chaîne d’une chaîne encodée en Base64, en décodant efficacement la chaîne Base64. Pensez à utiliser la fonction [base64ToString()](#base64ToString) plutôt que la fonction `decodeBase64()`. Bien que les deux fonctions agissent de manière identique, la fonction `base64ToString()` est préférée.

```
decodeBase64('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne encodée en Base64 à décoder | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="decodedatauri"></a>decodeDataUri

Retourne la version binaire d’un URI (Uniform Resource Identifier) de données. Pensez à utiliser la fonction [dataUriToBinary()](#dataUriToBinary) plutôt que la fonction `decodeDataUri()`. Bien que les deux fonctions agissent de manière identique, la fonction `dataUriToBinary()` est préférée.

```
decodeDataUri('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne d’URI de données à décoder | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="decodeuricomponent"></a>decodeUriComponent

Retourne une chaîne qui remplace les caractères d’échappement par des versions décodées. 

```
decodeUriComponent('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne contenant les caractères d’échappement à décoder | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="div"></a>div

Retourne l’entier résultant de la division de deux nombres. Pour obtenir le reste, consultez [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | OUI | Entier ou flottant | Nombre à diviser par le *diviseur* | 
| <*divisor*> | OUI | Entier ou flottant | Nombre qui divise le *dividende*, mais qui ne peut pas être égal à 0 | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*quotient-result*> | Entier  | Entier résultant de la division du premier nombre par le second nombre | 
|||| 

*Exemple*

Les deux exemples illustrent la division du premier nombre par le second :

```
div(10, 5)
div(11, 5)
```

Et retournent ce résultat : `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeUriComponent

Retourne une version encodée sous forme d’URI d’une chaîne en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement. Pensez à utiliser la fonction [uriComponent()](#uriComponent) plutôt que la fonction `encodeUriComponent()`. Bien que les deux fonctions fonctionnent de manière identique, `uriComponent()` est préféré.

```
encodeUriComponent('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne à convertir au format encodé sous forme d’URI | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="empty"></a>empty

Vérifie si une collection est vide. Retourne la valeur true lorsque la collection est vide ou la valeur false dans le cas contraire.

```
empty('<collection>')
empty([<collection>])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne, tableau ou objet | Collection à vérifier | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true lorsque la collection est vide. Retourne la valeur false lorsque la collection n’est pas vide. | 
|||| 

*Exemple* 

Ces exemples vérifient si les collections spécifiées sont vides :

```
empty('')
empty('abc')
```

Et retournent les résultats suivants : 

* Premier exemple : comme une chaîne vide est transmise, la fonction retourne `true`. 
* Second exemple : comme la chaîne « abc » est transmise, la fonction retourne `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

Vérifie si une chaîne se termine par une sous-chaîne spécifique. Retourne la valeur true lorsque la sous-chaîne est trouvée ou la valeur false lorsqu’elle est introuvable. Cette fonction ne respecte pas la casse.

```
endsWith('<text>', '<searchText>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne à vérifier | 
| <*searchText*> | OUI | Chaîne | Sous-chaîne de fin à rechercher | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false  | Booléen | Retourne la valeur true lorsque la sous-chaîne de fin est trouvée. Retourne la valeur false lorsqu’elle est introuvable. | 
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

## <a name="equals"></a>equals

Vérifie si les deux valeurs, expressions ou objets sont équivalents. Retourne la valeur true si les deux sont équivalents ou la valeur false s’ils ne le sont pas.

```
equals('<object1>', '<object2>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*> | OUI | Divers | Valeurs, expressions ou objets à comparer | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true lorsque les deux sont équivalents. Retourne la valeur false lorsque les deux ne sont pas équivalents. | 
|||| 

*Exemple*

Ces exemples vérifient si les entrées spécifiées sont équivalentes. 

```
equals(true, 1)
equals('abc', 'abcd')
```

Et retournent les résultats suivants : 

* Premier exemple : comme les deux valeurs sont équivalentes, la fonction retourne `true`.
* Second exemple : comme les deux valeurs ne sont pas équivalentes, la fonction retourne `false`.

<a name="first"></a>

## <a name="first"></a>first

Retourne le premier élément d’une chaîne ou d’un tableau.

```
first('<collection>')
first([<collection>])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne ou tableau | Collection dans laquelle rechercher le premier élément |
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*first-collection-item*> | Quelconque | Premier élément de la collection | 
|||| 

*Exemple*

Ces exemples illustrent la recherche du premier élément dans ces collections :

```
first('hello')
first([0, 1, 2])
```

Et retournent les résultats suivants : 

* Premier exemple : `"h"`
* Second exemple : `0`

<a name="float"></a>

## <a name="float"></a>float

Convertit une version de type chaîne d’un nombre à virgule flottante en nombre réel à virgule flottante. Vous pouvez utiliser cette fonction uniquement lors de la transmission de paramètres personnalisés à une application, telle qu’une application logique.

```
float('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne qui contient un nombre valide à virgule flottante à convertir |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="formatdatetime"></a>formatDateTime

Retourne un horodatage au format spécifié.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="formdatamultivalues"></a>formDataMultiValues

Retourne un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Action dont la sortie contient la valeur de clé souhaitée | 
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | Tableau | Tableau contenant toutes les valeurs qui correspondent à la clé spécifiée | 
|||| 

*Exemple* 

Cet exemple illustre la création d’un tableau à partir de la valeur de la clé « Subject » dans la sortie form-data ou form-encoded de l’action spécifiée :  

```
formDataMultiValues('Send_an_email', 'Subject')
```

Et retourne le texte de l’objet dans un tableau, par exemple : `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Retourne une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded* d’une action. Si la fonction trouve plusieurs correspondances, elle génère une erreur.

```
formDataValue('<actionName>', '<key>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Action dont la sortie contient la valeur de clé souhaitée | 
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="getfuturetime"></a>getFutureTime

Retourne l’horodatage actuel plus les unités de temps spécifiées.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | OUI | Entier  | Nombre d’unités de temps spécifiées à soustraire | 
| <*timeUnit*> | OUI | Chaîne | Unité de temps à utiliser avec le paramètre *interval* : « Second », « Minute », « Hour », « Day », « Week », « Month », « Year » | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | Chaîne | Horodatage actuel plus nombre d’unités de temps spécifié | 
|||| 

*Exemple 1*

Supposez que l’horodatage actuel soit « 2018-03-01T00:00:00.0000000Z ». Cet exemple illustre l’ajout de cinq jours à cet horodatage :

```
getFutureTime(5, 'Day')
```

Et retourne ce résultat : `"2018-03-06T00:00:00.0000000Z"`

*Exemple 2*

Supposez que l’horodatage actuel soit « 2018-03-01T00:00:00.0000000Z ». Cet exemple illustre l’ajout de cinq jours et convertit le résultat au format « D » :

```
getFutureTime(5, 'Day', 'D')
```

Et retourne ce résultat : `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Retourne l’horodatage actuel moins les unités de temps spécifiées.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | OUI | Entier  | Nombre d’unités de temps spécifiées à soustraire | 
| <*timeUnit*> | OUI | Chaîne | Unité de temps à utiliser avec le paramètre *interval* : « Second », « Minute », « Hour », « Day », « Week », « Month », « Year » | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | Chaîne | Horodatage actuel moins nombre d’unités de temps spécifié | 
|||| 

*Exemple 1*

Supposez que l’horodatage actuel soit « 2018-02-01T00:00:00.0000000Z ». Cet exemple illustre le retrait de cinq jours de cet horodatage :

```
getPastTime(5, 'Day')
```

Et retourne ce résultat : `"2018-01-27T00:00:00.0000000Z"`

*Exemple 2*

Supposez que l’horodatage actuel soit « 2018-02-01T00:00:00.0000000Z ». Cet exemple illustre le retrait de cinq jours et convertit le résultat au format « D » :

```
getPastTime(5, 'Day', 'D')
```

Et retourne ce résultat : `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>greater

Vérifie si la première valeur est supérieure à la seconde. Retourne la valeur true si la première valeur est supérieure ou la valeur false si elle est inférieure.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est supérieure à la seconde | 
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Valeur de comparaison | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true si la première valeur est supérieure à la seconde. Retourne la valeur false si la première valeur est égale ou inférieure à la seconde. | 
|||| 

*Exemple*

Ces exemples vérifient si la première valeur est supérieure à la seconde :

```
greater(10, 5)
greater('apple', 'banana')
```

Et retournent les résultats suivants : 

* Premier exemple : `true`
* Deuxième exemple : `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Vérifie si la première valeur est supérieure ou égale à la seconde.
Retourne la valeur true si la première valeur est supérieure ou égale à la seconde, ou la valeur false si la première valeur est inférieure à la seconde.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est supérieure ou égale à la seconde | 
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Valeur de comparaison | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true si la première valeur est supérieure ou égale à la seconde. Retourne la valeur false si la première valeur est inférieure à la seconde. | 
|||| 

*Exemple*

Ces exemples vérifient si la première valeur est supérieure ou égale à la seconde :

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Et retournent les résultats suivants : 

* Premier exemple : `true`
* Deuxième exemple : `false`

<a name="guid"></a>

## <a name="guid"></a>GUID

Génère un identificateur global unique (GUID) sous la forme d’une chaîne, par exemple « c2ecc88d-88c8-4096-912c-d6f2e2b138ce » : 

```
guid()
```

Vous pouvez également spécifier un format différent pour le GUID autre que le format par défaut, « D », qui correspond à 32 chiffres séparés par des traits d’union.

```
guid('<format>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | Non  | Chaîne | Un seul [spécificateur de format](https://msdn.microsoft.com/library/97af8hh4) pour le GUID retourné. Par défaut, il s’agit du format « D », mais vous pouvez utiliser « N », « D », « B », « P » ou « X ». | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="if"></a>if

Vérifie si une expression est vraie ou fausse. En fonction du résultat, retourne une valeur spécifiée.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | OUI | Booléen | Expression à vérifier | 
| <*valueIfTrue*> | OUI | Quelconque | Valeur à retourner lorsque l’expression est vraie | 
| <*valueIfFalse*> | OUI | Quelconque | Valeur à retourner lorsque l’expression est fausse | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*specified-return-value*> | Quelconque | Valeur spécifiée retournée selon que l’expression est vraie ou fausse | 
|||| 

*Exemple* 

Cet exemple retourne `"yes"`, car l’expression spécifiée retourne la valeur true. Dans le cas contraire, l’exemple retourne `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Retourne la position ou la valeur d’index de départ d’une sous-chaîne. Cette fonction ne respecte pas la casse, et les index commencent par 0. 

```
indexOf('<text>', '<searchText>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à rechercher | 
| <*searchText*> | OUI | Chaîne | Sous-chaîne à rechercher | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*index-value*>| Entier  | Position ou valeur d’index de départ de la sous-chaîne spécifiée. <p>Si la chaîne est introuvable, retourne le chiffre -1. </br>Si la chaîne est vide, retourne le chiffre 0. | 
|||| 

*Exemple* 

Cet exemple recherche la valeur d’index de départ de la sous-chaîne « world » dans la chaîne « hello world » :

```
indexOf('hello world', 'world')
```

Et retourne ce résultat : `6`

<a name="int"></a>

## <a name="int"></a>int

Retourne la version de type entier d’une chaîne.

```
int('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne à convertir | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*integer-result*> | Entier  | Version de type entier de la chaîne spécifiée | 
|||| 

*Exemple* 

Cet exemple illustre la création d’une version de type entier pour la chaîne « 10 » :

```
int('10')
```

Et retourne ce résultat : `10`

<a name="item"></a>

## <a name="item"></a>item

Lorsqu’elle est utilisée dans une action répétée d’un tableau, retourne l’élément actuel du tableau au cours de l’itération actuelle de l’action. Vous pouvez également obtenir les valeurs à partir des propriétés de cet élément. 

```
item()
```

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*current-array-item*> | Quelconque | Élément actuel dans le tableau correspondant à l’itération actuelle de l’action | 
|||| 

*Exemple* 

Cet exemple illustre l’obtention de l’élément `body` à partir du message actuel pour l’action « Send_an_email » au sein de l’itération actuelle d’une boucle for-each :

```
item().body
```

<a name="items"></a>

## <a name="items"></a>items

Retourne l’élément actuel à partir de chaque cycle d’une boucle for-each. Utilisez cette fonction à l’intérieur de la boucle for-each.

```
items('<loopName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | OUI | Chaîne | Nom de la boucle for-each | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*item*> | Quelconque | Élément à partir du cycle en cours de la boucle for-each spécifiée | 
|||| 

*Exemple* 

Cet exemple illustre l’obtention de l’élément actuel à partir de la boucle for-each spécifiée :

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>json

Retourne la valeur ou l’objet de type JavaScript Object Notation (JSON) d’une chaîne ou d’un élément XML.

```
json('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne ou élément XML | Chaîne ou élément XML à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="intersection"></a>intersection

Retourne une collection qui contient *uniquement* les éléments communs aux collections spécifiées. Pour qu’il apparaisse dans le résultat, un élément doit apparaître dans toutes les collections transmises à cette fonction. Si un ou plusieurs éléments portent le même nom, le dernier élément de ce nom apparaît dans le résultat.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>... | OUI | Tableau ou objet, mais pas les deux | Collections à partir desquelles vous souhaitez *uniquement* les éléments communs | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*common-items*> | Tableau ou objet, respectivement | Collection qui contient uniquement les éléments communs aux collections spécifiées | 
|||| 

*Exemple* 

Cet exemple recherche les éléments communs dans ces tableaux :  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

Et retourne un tableau comportant *uniquement* ces éléments : `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Retourne une chaîne qui contient tous les éléments d’un tableau, et dont tous les caractères sont séparés par un *séparateur*.

```
join([<collection>], '<delimiter>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Tableau | Tableau qui comporte les éléments à joindre |  
| <*delimiter*> | OUI | Chaîne | Séparateur affiché entre chaque caractère de la chaîne obtenue | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Chaîne | Chaîne obtenue créée à partir de tous les éléments du tableau spécifié |
|||| 

*Exemple* 

Cet exemple illustre la création d’une chaîne à partir de tous les éléments de ce tableau avec le caractère spécifié comme séparateur :

```
join([a, b, c], '.')
```

Et retourne ce résultat : `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>last

Retourne le dernier élément d’une collection.

```
last('<collection>')
last([<collection>])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne ou tableau | Collection dans laquelle rechercher le dernier élément | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*last-collection-item*> | Chaîne ou tableau, respectivement | Dernier élément de la collection | 
|||| 

*Exemple* 

Ces exemples illustrent la recherche du dernier élément dans ces collections :

```
last('abcd')
last([0, 1, 2, 3])
```

Et retournent les résultats suivants : 

* Premier exemple : `"d"`
* Second exemple : `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Retourne la position ou la valeur d’index de fin d’une sous-chaîne. Cette fonction ne respecte pas la casse, et les index commencent par 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à rechercher | 
| <*searchText*> | OUI | Chaîne | Sous-chaîne à rechercher | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*ending-index-value*> | Entier  | Position ou valeur d’index de fin de la sous-chaîne spécifiée. <p>Si la chaîne est introuvable, retourne le chiffre -1. </br>Si la chaîne est vide, retourne le chiffre 0. | 
|||| 

*Exemple* 

Cet exemple recherche la valeur d’index de fin de la sous-chaîne « world » dans la chaîne « hello world » :

```
lastIndexOf('hello world', 'world')
```

Et retourne ce résultat : `10`

<a name="length"></a>

## <a name="length"></a>length

Retourne le nombre d’éléments d’une collection.

```
length('<collection>')
length([<collection>])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne ou tableau | Collection contenant les éléments à comptabiliser | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*length-or-count*> | Entier  | Nombre d’éléments de la collection | 
|||| 

*Exemple*

Ces exemples illustrent le comptage du nombre d’éléments contenus dans ces collections : 

```
length('abcd')
length([0, 1, 2, 3])
```

Et retournent ce résultat : `4`

<a name="less"></a>

## <a name="less"></a>less

Vérifie si la première valeur est inférieure à la seconde.
Retourne la valeur true si la première valeur est inférieure à la seconde, ou la valeur false si la première valeur est supérieure à la seconde.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est inférieure à la seconde | 
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Élément de comparaison | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true si la première valeur est inférieure à la seconde. Retourne la valeur false si la première valeur est égale ou supérieure à la seconde. | 
|||| 

*Exemple*

Ces exemples vérifient si la première valeur est inférieure à la seconde.

```
less(5, 10)
less('banana', 'apple')
```

Et retournent les résultats suivants : 

* Premier exemple : `true`
* Deuxième exemple : `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Vérifie si la première valeur est inférieure ou égale à la seconde.
Retourne la valeur true si la première valeur est inférieure ou égale à la seconde, ou la valeur false si la première valeur est supérieure à la seconde.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Entier, flottant ou chaîne | Première valeur à vérifier pour déterminer si elle est inférieure ou égale à la seconde | 
| <*compareTo*> | OUI | Respectivement entier, flottant ou chaîne | Élément de comparaison | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false  | Booléen | Retourne la valeur true si la première valeur est inférieure ou égale à la seconde. Retourne la valeur false si la première valeur est supérieure à la seconde. |  
|||| 

*Exemple*

Ces exemples vérifient si la première valeur est inférieure ou égale à la seconde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Et retournent les résultats suivants : 

* Premier exemple : `true`
* Deuxième exemple : `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Retourne « l’URL de rappel » qui appelle un déclencheur ou une action. Cette fonction fonctionne uniquement avec les déclencheurs et actions pour les types de connecteur **HttpWebhook** et **ApiConnectionWebhook**, mais pas les types **Manual**, **Recurrence**, **HTTP** ni **APIConnection**. 

```
listCallbackUrl()
```

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*callback-URL*> | Chaîne | URL de rappel d’un déclencheur ou d’une action |  
|||| 

*Exemple*

Cet exemple illustre un exemple d’URL de rappel que cette fonction peut retourner :

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

Retourne la valeur la plus élevée d’une liste ou d’un tableau de nombres incluse aux deux extrémités. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>... | OUI | Entier, flottant ou les deux | Ensemble de nombres dont vous voulez la valeur la plus élevée | 
| [<*number1*>, <*number2*>...] | OUI | Tableau : entier, flottant ou les deux | Tableau de nombres dont vous voulez la valeur la plus élevée | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*max-value*> | Entier ou flottant | Valeur la plus élevée dans le tableau ou l’ensemble de nombres spécifié | 
|||| 

*Exemple* 

Ces exemples illustrent l’obtention de la valeur la plus élevée de l’ensemble des nombres et du tableau :

```
max(1, 2, 3)
max([1, 2, 3])
```

Et retournent ce résultat : `3`

<a name="min"></a>

## <a name="min"></a>min

Retourne la plus petite valeur d’un ensemble de nombres ou d’un tableau.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>... | OUI | Entier, flottant ou les deux | Ensemble de nombres dont vous voulez la valeur la plus petite | 
| [<*number1*>, <*number2*>...] | OUI | Tableau : entier, flottant ou les deux | Tableau de nombres dont vous voulez la valeur la plus petite | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*min-value*> | Entier ou flottant | Valeur la plus petite dans le tableau ou l’ensemble de nombres spécifiés | 
|||| 

*Exemple* 

Ces exemples illustrent l’obtention de la valeur la plus petite de l’ensemble des nombres et du tableau :

```
min(1, 2, 3)
min([1, 2, 3])
```

Et retournent ce résultat : `1`

<a name="mod"></a>

## <a name="mod"></a>mod

Retourne le reste de la division de deux nombres. Pour obtenir le résultat sous forme d’un entier, consultez [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | OUI | Entier ou flottant | Nombre à diviser par le *diviseur* | 
| <*divisor*> | OUI | Entier ou flottant | Nombre qui divise le *dividende*, mais qui ne peut pas être égal à 0. | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="mul"></a>mul

Retourne le produit de la multiplication de deux nombres.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | OUI | Entier ou flottant | Nombre à multiplier par *multiplicand2* | 
| <*multiplicand2*> | OUI | Entier ou flottant | Nombre qui multiplie *multiplicand1* | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="multipartbody"></a>multipartBody

Retourne le corps correspondant à une partie spécifique dans la sortie d’une action qui comporte plusieurs parties.

```
multipartBody('<actionName>', <index>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | OUI | Chaîne | Nom de l’action dont la sortie compte plusieurs parties | 
| <*index*> | OUI | Entier  | Valeur d’index pour la partie que vous souhaitez | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*body*> | Chaîne | Corps de la partie spécifiée | 
|||| 

<a name="not"></a>

## <a name="not"></a>not

Vérifie si une expression est fausse. Retourne la valeur true lorsque l’expression est fausse, ou la valeur false lorsque l’expression est vraie.

```
not(<expression>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | OUI | Booléen | Expression à vérifier | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true lorsque l’expression est fausse. Retourne la valeur false lorsque l’expression est vraie. |  
|||| 

*Exemple 1*

Ces exemples vérifient si les expressions spécifiées sont fausses : 

```
not(false)
not(true)
```

Et retournent les résultats suivants :

* Premier exemple : comme l’expression est fausse, la fonction retourne `true`.
* Second exemple : comme l’expression est vraie, la fonction retourne `false`.

*Exemple 2*

Ces exemples vérifient si les expressions spécifiées sont fausses : 

```
not(equals(1, 2))
not(equals(1, 1))
```

Et retournent les résultats suivants :

* Premier exemple : comme l’expression est fausse, la fonction retourne `true`.
* Second exemple : comme l’expression est vraie, la fonction retourne `false`.

<a name="or"></a>

## <a name="or"></a>or

Vérifie si au moins une expression est vraie. Retourne la valeur true si au moins une expression est vraie ou la valeur false si toutes les expressions sont fausses.

```
or(<expression1>, <expression2>, ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>... | OUI | Booléen | Expressions à vérifier | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false | Booléen | Retourne la valeur true si au moins une expression est vraie. Retourne la valeur false lorsque toutes les expressions sont fausses. |  
|||| 

*Exemple 1*

Ces exemples vérifient si au moins une expression est vraie :

```
or(true, false)
or(false, false)
```

Et retournent les résultats suivants :

* Premier exemple : comme au moins une expression est vraie, la fonction retourne `true`.
* Second exemple : comme les deux expressions sont fausses, la fonction retourne `false`.

*Exemple 2*

Ces exemples vérifient si au moins une expression est vraie :

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Et retournent les résultats suivants :

* Premier exemple : comme au moins une expression est vraie, la fonction retourne `true`.
* Second exemple : comme les deux expressions sont fausses, la fonction retourne `false`.

<a name="parameters"></a>

## <a name="parameters"></a>parameters

Retourne la valeur d’un paramètre décrit dans la définition de votre application logique. 

```
parameters('<parameterName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*parameterName*> | OUI | Chaîne | Nom du paramètre dont vous voulez la valeur | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="rand"></a>rand

Retourne un entier aléatoire à partir d’une plage spécifiée, qui est inclus uniquement au point de départ.

```
rand(<minValue>, <maxValue>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*minValue*> | OUI | Entier  | Entier le plus petit de la plage | 
| <*maxValue*> | OUI | Entier  | Entier qui suit l’entier le plus élevé dans la plage que la fonction peut retourner | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*random-result*> | Entier  | Entier aléatoire retourné à partir de la plage spécifiée |  
|||| 

*Exemple*

Cet exemple illustre l’obtention d’un entier aléatoire à partir de la plage spécifiée, en excluant la valeur maximale : 

```
rand(1, 5)
```

Et retourne l’un de ces nombres comme résultat : `1`, `2`, `3` ou `4` 

<a name="range"></a>

## <a name="range"></a>range

Retourne un tableau d’entiers qui commence par un entier spécifique.

```
range(<startIndex>, <count>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*startIndex*> | OUI | Entier  | Valeur entière qui constitue le premier élément du tableau | 
| <*count*> | OUI | Entier  | Nombre d’entiers du tableau | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*range-result*>] | Tableau | Tableau dont les entiers commencent à partir de l’index spécifié |  
|||| 

*Exemple*

Cet exemple illustre la création d’un tableau d’entiers qui commence à partir de l’index spécifié et qui possède le nombre spécifié d’entiers :

```
range(1, 4)
```

Et retourne ce résultat : `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>remplacer

Remplace une sous-chaîne par la chaîne spécifiée et retourne la chaîne de résultat. Cette fonction respecte la casse.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne comportant la sous-chaîne à remplacer | 
| <*oldText*> | OUI | Chaîne | Sous-chaîne à remplacer | 
| <*newText*> | OUI | Chaîne | Chaîne de remplacement | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="removeproperty"></a>removeProperty

Supprime une propriété dans un objet et retourne l’objet mis à jour.

```
removeProperty(<object>, '<property>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | OUI | Object | Objet JSON dont vous souhaitez supprimer une propriété | 
| <*property*> | OUI | Chaîne | Nom de la propriété à supprimer | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | Objet JSON mis à jour sans la propriété spécifiée | 
|||| 

*Exemple*

Cet exemple illustre la suppression de la propriété `"accountLocation"` dans un objet `"customerProfile"`, qui est converti au format JSON avec la fonction [JSON()](#json), et retourne l’objet mis à jour :

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>SetProperty

Définit la valeur d’une propriété d’un objet et retourne l’objet mis à jour. Pour ajouter une nouvelle propriété, vous pouvez utiliser cette fonction ou la fonction [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | OUI | Object | Objet JSON dont vous souhaitez définir la propriété | 
| <*property*> | OUI | Chaîne | Nom de la propriété existante ou nouvelle à définir | 
| <*value*> | OUI | Quelconque | Valeur à définir pour la propriété spécifiée |
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | Objet JSON mis à jour dont vous définissez la propriété | 
|||| 

*Exemple*

Cet exemple illustre la définition de la propriété `"accountNumber"` dans un objet `"customerProfile"`, qui est converti au format JSON avec la fonction [JSON()](#json). La fonction attribue une valeur générée par la fonction [guid()](#guid), et retourne l’objet JSON mis à jour :

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>skip

Supprime des éléments du début d’une collection et retourne *tous les autres* éléments.

```
skip([<collection>], <count>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Tableau | Collection dont vous voulez supprimer des éléments | 
| <*count*> | OUI | Entier  | Entier positif correspondant au nombre d’éléments à supprimer au début | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*updated-collection*>] | Tableau | Collection mise à jour à l’issue de la suppression des éléments spécifiés | 
|||| 

*Exemple*

Cet exemple illustre la suppression d’un élément, le nombre 0, au début du tableau spécifié : 

```
skip([0, 1, 2, 3], 1)
```

Et retourne ce tableau avec les éléments restants : `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Retourne un tableau qui contient tous les caractères d’une chaîne, et dont tous les caractères sont séparés par un *séparateur*.

```
split('<text>', '<separator>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne qui comporte les caractères à fractionner |  
| <*separator*> | OUI | Chaîne | Séparateur affiché entre chaque caractère du tableau obtenu | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*char1*><*separator*><*char2*><*separator*>...] | Tableau | Tableau obtenu créé à partir de tous les éléments de la chaîne spécifiée |
|||| 

*Exemple* 

Cet exemple illustre la création d’un tableau à partir de la chaîne spécifiée, en séparant chaque caractère par une virgule comme séparateur :

```
split('abc', ',')
```

Et retourne ce résultat : `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Retourne le début du jour d’un horodatage. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="startofhour"></a>startOfHour

Retourne le début de l’heure d’un horodatage. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="startofmonth"></a>startOfMonth

Retourne le début du mois pour un horodatage. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. |
||||| 

| Valeur de retour | type | Description | 
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

## <a name="startswith"></a>startsWith

Vérifie si une chaîne commence par une sous-chaîne spécifique. Retourne la valeur true lorsque la sous-chaîne est trouvée ou la valeur false lorsqu’elle est introuvable. Cette fonction ne respecte pas la casse.

```
startsWith('<text>', '<searchText>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne à vérifier | 
| <*searchText*> | OUI | Chaîne | Chaîne de départ à rechercher | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| true ou false  | Booléen | Retourne la valeur true lorsque la sous-chaîne de début est trouvée. Retourne la valeur false lorsqu’elle est introuvable. | 
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

## <a name="string"></a>chaîne

Retourne la version de type chaîne d’une valeur.

```
string(<value>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Quelconque | Valeur à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="sub"></a>sub

Retourne le résultat de la soustraction du second nombre du premier.

```
sub(<minuend>, <subtrahend>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | OUI | Entier ou flottant | Nombre à partir duquel soustraire le *plus petit terme* | 
| <*subtrahend*> | OUI | Entier ou flottant | Nombre à soustraire du *plus petit terme* | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="substring"></a>substring

Retourne les caractères d’une chaîne, en commençant à partir de la position spécifiée ou de l’index. Les valeurs d’index commencent par le chiffre 0. 

```
substring('<text>', <startIndex>, <length>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne dont vous souhaitez les caractères | 
| <*startIndex*> | OUI | Entier  | Nombre positif correspondant à la position de départ, ou valeur d’index | 
| <*length*> | OUI | Entier  | Nombre positif de caractères que vous souhaitez dans la sous-chaîne | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="subtractfromtime"></a>subtractFromTime

Soustrait un nombre d’unités de temps d’un horodatage. Voir aussi [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne qui contient l’horodatage | 
| <*interval*> | OUI | Entier  | Nombre d’unités de temps spécifiées à soustraire | 
| <*timeUnit*> | OUI | Chaîne | Unité de temps à utiliser avec le paramètre *interval* : « Second », « Minute », « Hour », « Day », « Week », « Month », « Year » | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="take"></a>take

Retourne des éléments du début d’une collection. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | OUI | Chaîne ou tableau | Collection dont vous souhaitez des éléments | 
| <*count*> | OUI | Entier  | Entier positif correspondant au nombre d’éléments que vous souhaitez à partir du début | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*subset*> ou [<*subset*>] | Chaîne ou tableau, respectivement | Chaîne ou tableau qui comporte le nombre spécifié d’éléments obtenus à partir du début de la collection d’origine | 
|||| 

*Exemple*

Ces exemples illustrent l’obtention du nombre spécifié d’éléments à partir du début de ces collections :

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

Et retournent les résultats suivants :

* Premier exemple : `"abc"`
* Second exemple : `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>ticks

Retourne la valeur de la propriété `ticks` pour un horodatage spécifique. Une *graduation* est un intervalle de 100 nanosecondes.

```
ticks('<timestamp>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | OUI | Chaîne | Chaîne d’un horodatage | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*ticks-number*> | Entier  | Nombre de graduations depuis l’horodatage spécifié | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Retourne une chaîne en minuscules. Si un caractère de la chaîne n’a pas de version en minuscules, ce caractère reste tel quel dans la chaîne retournée.

```
toLower('<text>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne à retourner en minuscules | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="toupper"></a>toUpper

Retourne une chaîne en majuscules. Si un caractère de la chaîne n’a pas de version en majuscules, ce caractère reste tel quel dans la chaîne retournée.

```
toUpper('<text>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne à retourner en majuscules | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="trigger"></a>trigger

Retourne la sortie d’un déclencheur lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON que vous pouvez attribuer à une expression. 

* Dans les entrées d’un déclencheur, cette fonction retourne la sortie de l’exécution précédente. 

* Dans une condition d’un déclencheur, cette fonction retourne la sortie de l’exécution actuelle. 

Par défaut, la fonction fait référence à l’objet de déclencheur dans son intégralité, mais vous pouvez éventuellement spécifier une propriété dont vous souhaitez la valeur. En outre, des versions raccourcies sont disponibles pour cette fonction. Voir [triggerOutputs()](#triggerOutputs) et [triggerBody()](#triggerBody). 

```
trigger()
```

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | Chaîne | Sortie d’un déclencheur à l’exécution | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Retourne la sortie `body` d’un déclencheur lors de l’exécution. Raccourci de `trigger().outputs.body`. Voir [trigger()](#trigger). 

```
triggerBody()
```

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*trigger-body-output*> | Chaîne | Sortie `body` du déclencheur | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retourne un tableau contenant les valeurs qui correspondent à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur. 

```
triggerFormDataMultiValues('<key>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | Tableau | Tableau contenant toutes les valeurs qui correspondent à la clé spécifiée | 
|||| 

*Exemple* 

Cet exemple illustre la création d’un tableau à partir de la valeur de la clé « feedUrl » dans la sortie form-data ou form-encoded d’un déclencheur RSS : 

```
triggerFormDataMultiValues('feedUrl')
```

Et retourne ce tableau en tant qu’exemple de résultat : `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Retourne une chaîne contenant une valeur unique qui correspond à un nom de clé dans la sortie *form-data* ou *form-encoded* d’un déclencheur. Si la fonction trouve plusieurs correspondances, elle génère une erreur.

```
triggerFormDataValue('<key>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | OUI | Chaîne | Nom de la clé dont vous souhaitez la valeur |
||||| 

| Valeur de retour | type | Description | 
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

Retourne le corps correspondant à une partie spécifique dans la sortie d’un déclencheur qui comporte plusieurs parties. 

```
triggerMultipartBody(<index>)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*index*> | OUI | Entier  | Valeur d’index pour la partie que vous souhaitez |
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*body*> | Chaîne | Corps de la partie spécifiée dans la sortie en plusieurs parties d’un déclencheur | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Retourne la sortie d’un déclencheur lors de l’exécution ou les valeurs d’autres paires nom-valeur JSON. Raccourci de `trigger().outputs`. Voir [trigger()](#trigger). 

```
triggerOutputs()
```

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | Chaîne | Sortie d’un déclencheur à l’exécution  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>découper

Supprime les espaces blancs de début et de fin d’une chaîne et retourne la chaîne mise à jour.

```
trim('<text>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | OUI | Chaîne | Chaîne contenant les espaces blancs de début et de fin à supprimer | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="union"></a>union

Retourne une collection qui contient *tous* les éléments des collections spécifiées. Pour qu’il apparaisse dans le résultat, un élément peut apparaître dans n’importe quelle collection transmise à cette fonction. Si un ou plusieurs éléments portent le même nom, le dernier élément de ce nom apparaît dans le résultat. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>...  | OUI | Tableau ou objet, mais pas les deux | Collections dont vous souhaitez *tous* les éléments | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Tableau ou objet, respectivement | Collection qui contient tous les éléments des collections spécifiées (pas de doublons) | 
|||| 

*Exemple* 

Cet exemple illustre l’obtention de *tous* les éléments de ces collections : 

```
union([1, 2, 3], [1, 2, 10, 101])
```

Et retourne ce résultat : `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Retourne une version encodée sous forme d’URI d’une chaîne en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement. Utilisez cette fonction plutôt que la fonction [encodeUriComponent()](#encodeUriComponent). Bien que les deux fonctions fonctionnent de manière identique, `uriComponent()` est préféré.

```
uriComponent('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne à convertir au format encodé sous forme d’URI | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourne la version binaire d’un composant d’URI.

```
uriComponentToBinary('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne encodée sous forme d’URI à convertir | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="uricomponenttostring"></a>uriComponentToString

Retourne la version de type chaîne d’une chaîne encodée sous forme d’URI, en décodant efficacement la chaîne encodée sous forme d’URI.

```
uriComponentToString('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne encodée sous forme d’URI à décoder | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="urihost"></a>uriHost

Retourne la valeur `host` pour un URI.

```
uriHost('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `host` | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="uripath"></a>uriPath

Retourne la valeur `path` pour un URI. 

```
uriPath('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `path` | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="uripathandquery"></a>uriPathAndQuery

Retourne les valeurs `path` et `query` pour un URI.

```
uriPathAndQuery('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez les valeurs `path` et `query` | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="uriport"></a>uriPort

Retourne la valeur `port` pour un URI.

```
uriPort('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `port` | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*port-value*> | Entier  | Valeur `port` de l’URI spécifié. Si `port` ne spécifie pas de valeur, retourne le port par défaut du protocole. | 
|||| 

*Exemple*

Cet exemple retourne la valeur `port` pour cet URI :

```
uriPort('http://www.localhost:8080')
```

Et retourne ce résultat : `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Retourne la valeur `query` pour un URI.

```
uriQuery('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `query` | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="urischeme"></a>uriScheme

Retourne la valeur `scheme` pour un URI.

```
uriScheme('<uri>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | OUI | Chaîne | URI dont vous souhaitez la valeur `scheme` | 
||||| 

| Valeur de retour | type | Description | 
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

## <a name="utcnow"></a>utcNow

Retourne l’horodatage actuel. 

```
utcNow('<format>')
```

Si vous le souhaitez, vous pouvez spécifier un autre format avec le paramètre <*format*>. 


| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | Non  | Chaîne | [Spécificateur de format unique](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [modèle de format personnalisé](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Le format par défaut de l’horodatage est [« o »](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-jjT:mm:ss:fffffffK), qui est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et conserve les informations de fuseau horaire. | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*current-timestamp*> | Chaîne | Date et heure actuelles | 
|||| 

*Exemple 1*

Supposons que nous soyons le 15 avril 2018 à 13:00:00. Cet exemple illustre l’obtention de l’horodatage actuel : 

```
utcNow()
```

Et retourne ce résultat : `"2018-04-15T13:00:00.0000000Z"`

*Exemple 2*

Supposons que nous soyons le 15 avril 2018 à 13:00:00. Cet exemple illustre l’obtention de l’horodatage actuel en utilisant le format « D » facultatif :

```
utcNow('D')
```

Et retourne ce résultat : `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>variables

Retourne la valeur d’une variable spécifiée. 

```
variables('<variableName>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*variableName*> | OUI | Chaîne | Nom de la variable dont vous souhaitez la valeur | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*variable-value*> | Quelconque | Valeur de la variable spécifiée | 
|||| 

*Exemple*

Supposons que la valeur actuelle de la variable « numItems » corresponde à 20. Cet exemple illustre l’obtention de la valeur entière de cette variable :

```
variables('numItems')
```

Et retourne ce résultat : `20`

<a name="workflow"></a>

## <a name="workflow"></a>flux de travail

Retourne tous les détails sur le flux de travail proprement dit pendant l’exécution. 

```
workflow().<property>
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | Non  | Chaîne | Nom de la propriété de flux de travail dont vous souhaitez la valeur <p>Un objet de flux de travail possède ces propriétés : **name**, **type**, **id**, **location** et **run**. La valeur de la propriété **run** est également un objet qui possède ces propriétés : **name**, **type** et **id**. | 
||||| 

*Exemple*

Cet exemple retourne le nom de l’exécution actuelle d’un flux de travail :

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Retourne la version XML d’une chaîne qui contient un objet JSON. 

```
xml('<value>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | OUI | Chaîne | Chaîne contenant l’objet JSON à convertir <p>L’objet JSON ne doit posséder qu’une seule propriété racine. <br>Utilisez le caractère de barre oblique inverse (\\) comme caractère d’échappement pour le guillemet double ("). | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*xml-version*> | Object | Élément XML encodé correspondant à la chaîne ou à l’objet JSON spécifié | 
|||| 

*Exemple 1*

Cet exemple illustre la création de la version XML de cette chaîne, qui contient un objet JSON : 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

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

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

Et retourne le résultat XML suivant : 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>xpath

Vérifie si le code XML contient des valeurs ou des nœuds qui correspondent à une expression de langage XPath et retourne les valeurs ou les nœuds correspondants. Une expression XPath, ou simplement « XPath », vous permet de parcourir une structure de document XML afin de pouvoir sélectionner des nœuds ou des valeurs de calcul dans le contenu XML.

```
xpath('<xml>', '<xpath>')
```

| Paramètre | Obligatoire | type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*xml*> | OUI | Quelconque | Chaîne XML pour rechercher des nœuds ou des valeurs qui correspondent à une valeur d’expression XPath | 
| <*xpath*> | OUI | Quelconque | Expression XPath utilisée pour rechercher des nœuds ou des valeurs XML correspondants | 
||||| 

| Valeur de retour | type | Description | 
| ------------ | ---- | ----------- | 
| <*xml-node*> | XML | Nœud XML lorsqu’un seul nœud correspond à l’expression XPath spécifiée | 
| <*value*> | Quelconque | Valeur d’un nœud XML lorsqu’un seul nœud correspond à l’expression XPath spécifiée | 
| [<*xml-node1*>, <*xml-node2*>...] </br>-ou- </br>[<*value1*>, <*value2*>...] | Tableau | Tableau contenant des nœuds ou valeurs XML qui correspondent à l’expression XPath spécifiée | 
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

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Voici les arguments :

* Ce code XML, qui inclut l’espace de noms du document XML, `xmlns="http://contoso.com"` : 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* L’une ou l’autre des expressions XPath ici :

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Voici le nœud obtenu qui correspond au nœud `<location></location` :

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemple 4*

À la suite de l’exemple 3, cet exemple recherche la valeur du nœud `<location></location>` : 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Et retourne ce résultat : `"Paris"`

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [langage de définition du flux de travail](../logic-apps/logic-apps-workflow-definition-language.md)
