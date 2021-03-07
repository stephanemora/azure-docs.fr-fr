---
title: Filtrage d’événements pour Azure Event Grid
description: Explique comment filtrer des événements lors de la création d’un abonnement Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 94445341891149d5d02c7f33caef20bf45123e9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197773"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Comprendre le filtrage d’événements pour les abonnements Event Grid

Cet article décrit les différentes façons de filtrer les événements qui sont envoyés à votre point de terminaison. Quand vous créez un abonnement à un événement, trois options de filtrage s’offrent à vous :

* Types d’événements
* Objet commençant ou se terminant par
* Opérateurs et champs avancés

## <a name="event-type-filtering"></a>Filtrage par type d’événement

Par défaut, tous les [types d’événements](event-schema.md) pour la source de l’événement sont envoyés au point de terminaison. Vous pouvez décider d’envoyer uniquement certains types d’événements à votre point de terminaison. Par exemple, vous pouvez être notifié des mises à jour de vos ressources, mais ne pas être notifié d’autres opérations comme les suppressions. Dans ce cas, effectuez le filtrage par type d’événement `Microsoft.Resources.ResourceWriteSuccess`. Fournissez un tableau avec les types d’événements, ou spécifiez `All` pour obtenir tous les types d’événements de la source de l’événement.

La syntaxe JSON pour le filtrage par type d’événement est :

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrage par objet

Pour le filtrage simple par objet, spécifiez une valeur de départ ou de fin pour l’objet. Par exemple, vous pouvez spécifier que l’objet se termine par `.txt` pour obtenir uniquement les événements liés au chargement d’un fichier texte sur le compte de stockage. Ou vous pouvez filtrer sur l’objet commençant par `/blobServices/default/containers/testcontainer` pour obtenir tous les événements pour ce conteneur, mais pas pour d’autres conteneurs du compte de stockage.

Lorsque vous publiez des événements dans des rubriques personnalisées, créez des objets pour vos événements permettant aux abonnés de savoir facilement si l’événement les intéresse. Les abonnés utilisent la propriété d’objet pour filtrer et router des événements. Envisagez d’ajouter le chemin à l’origine de l’événement, de sorte que les abonnés puissent filtrer sur des segments de ce chemin. Le chemin d’accès permet aux abonnés de filtrer les événements avec précision ou à grande échelle. Si vous fournissez un chemin de trois segments comme `/A/B/C` dans l’objet, les abonnés peuvent filtrer sur le premier segment `/A` pour obtenir un vaste ensemble d’événements. Ces abonnés obtiennent des événements avec des objets tels que `/A/B/C` ou `/A/D/E`. Les autres abonnés peuvent filtrer par `/A/B` pour obtenir un ensemble plus restreint d’événements.

La syntaxe JSON pour le filtrage par objet est :

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrage avancé

Pour filtrer sur des valeurs dans les champs de données et spécifier l’opérateur de comparaison, utilisez l’option de filtrage avancé. Dans le filtrage avancé, vous spécifiez les éléments suivants :

* type d'opérateur : type de comparaison.
* clé : champ des données d’événement que vous utilisez pour le filtrage. Il peut s’agir d’un nombre, d’une valeur booléenne, d’une chaîne ou d’un tableau.
* valeurs : valeur ou valeurs à comparer à la clé.

## <a name="key"></a>Clé
La clé est le champ des données d’événement que vous utilisez pour le filtrage. Les types possibles sont les suivants :

- Number
- Boolean
- String
- Array. Vous devez définir la propriété `enableAdvancedFilteringOnArrays` sur true pour utiliser cette fonctionnalité. Actuellement, le portail Azure ne prend pas en charge l’activation de cette fonctionnalité. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Pour les événements dans le **schéma Event Grid**, utilisez les valeurs suivantes pour la clé : `ID`, `Topic`, `Subject`, `EventType`, `DataVersion` ou événement de donnée (comme `data.key1`).

Pour les événements dans le **schéma des événements cloud**, utilisez les valeurs suivantes pour la clé : `eventid`, `source`, `eventtype`, `eventtypeversion` ou événement de données (comme `data.key1`).

Pour le **schéma d’entrée personnalisé**, utilisez les champs de données d’événement (comme `data.key1`). Pour accéder aux champs de la section de données, utilisez la notation `.` (point). Par exemple, `data.sitename`, `data.appEventTypeDetail.action` pour accéder à `sitename` ou `action` pour l’exemple d’événement suivant.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Valeurs
Les valeurs peuvent être : nombre, chaîne, booléen ou tableau

## <a name="operators"></a>Opérateurs

Les opérateurs disponibles pour les **nombres** sont les suivants :

## <a name="numberin"></a>NumberIn
L’opérateur NumberIn prend la valeur true si la valeur de **clé** est l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` est 5 ou 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a, b, c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
L’opérateur NumberNotIn prend la valeur true si la valeur de **clé** n’est **pas** l’une des valeurs de **filere** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` n’est pas 41 ou 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a, b, c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
L’opérateur NumberLessThan prend la valeur true si la valeur de **clé** est **inférieure** à la valeur de **filtre** spécifiée. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` est inférieure à 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport à la valeur de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
L’opérateur NumberGreaterThan prend la valeur true si la valeur de **clé** est **supérieure** à la valeur de **filtre** spécifiée. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` est supérieure à 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport à la valeur de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
L’opérateur NumberLessThanOrEquals prend la valeur true si la valeur de **clé** est **inférieure ou égale** à la valeur de **filtre** spécifiée. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` est inférieure ou égale à 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport à la valeur de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
L’opérateur NumberGreaterThanOrEquals prend la valeur true si la valeur de **clé** est **supérieure ou égale** à la valeur de **filtre** spécifiée. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `counter` dans la section `data` est supérieure ou égale à 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport à la valeur de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
L’opérateur NumberInRange prend la valeur true si la valeur de **clé** est dans l’une des **plages de filtres** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` est dans l’une de ces deux plages : 3.14159 - 999.95, 3000 - 4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

La propriété `values` est un tableau de plages. Dans l’exemple précédent, il s’agit d’un tableau de deux plages. Voici un exemple de tableau avec une plage à vérifier. 

**Tableau avec une plage :** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : un tableau de plages. Dans ce pseudo-code, `a` et `b` sont des valeurs basses et élevées de chaque plage du tableau. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
L’opérateur NumberNotInRange prend la valeur true si la valeur de **clé** n’est **pas** dans l’une des **plages de filtres** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` est dans l’une de ces deux plages : 3.14159 - 999.95, 3000 - 4000. Si c’est le cas, l’opérateur retourne false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
La propriété `values` est un tableau de plages. Dans l’exemple précédent, il s’agit d’un tableau de deux plages. Voici un exemple de tableau avec une plage à vérifier.

**Tableau avec une plage :** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : un tableau de plages. Dans ce pseudo-code, `a` et `b` sont des valeurs basses et élevées de chaque plage du tableau. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


L’opérateur disponible pour les valeurs **booléennes** est : 

## <a name="boolequals"></a>BoolEquals
L’opérateur BoolEquals prend la valeur true si la valeur de **clé** est le **filtre** de valeur booléenne spécifié. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `isEnabled` dans la section `data` est `true`. 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport à la valeur booléenne du filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Les opérateurs disponibles pour les **chaînes** sont les suivants :

## <a name="stringcontains"></a>StringContains
L’opérateur **StringContains** prend la valeur true si la valeur de **clé** **contient** une des valeurs de **filtre** spécifiées (sous forme de substrings). Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` contient l’une des substrings spécifiées : `microsoft` ou `azure`. Par exemple, `azure data factory` contient `azure`. 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
L’opérateur **StringNotContains** prend la valeur true si la **clé** ne **contient pas** les valeurs de **filtre** spécifiées sous la forme de substrings. Si la clé contient une des valeurs spécifiées sous la forme d’une substring, l’opérateur prend la valeur false. Dans l’exemple suivant, l’opérateur retourne true uniquement si la valeur de l'attribut `key1` dans la section `data` ne contient pas `contoso` et `fabrikam` comme substrings. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
L’opérateur **StringBeginsWith** prend la valeur true si la valeur de **clé** **commence par** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` commence par `event` ou `grid`. Par exemple, `event hubs` commence par `event`.  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
L’opérateur **StringNotBeginsWith** prend la valeur true si la valeur de **clé** **ne commence pas par** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` commence par `event` ou `message`.

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
L’opérateur **StringEndsWith** prend la valeur true si la valeur de **clé** **se termine par** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` se termine par `jpg` ou `jpeg` ou `png`. Par exemple, `eventgrid.png` se termine par `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
L’opérateur **StringNotEndsWith** prend la valeur true si la valeur de **clé** **ne se termine pas par** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` ne se termine pas par `jpg` ou `jpeg` ou `png`. 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
L’opérateur **StringIn** vérifie si la valeur de **clé** **correspond exactement à** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` est `exact` ou `string` ou `matches`. 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
L’opérateur **StringNotIn** vérifie si la valeur de **clé** **ne correspond à** l’une des valeurs de **filtre** spécifiées. Dans l’exemple suivant, il vérifie si la valeur de l'attribut `key1` dans la section `data` n’est pas `aws` et `bridge`. 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Si la clé est un tableau, toutes les valeurs du tableau sont vérifiées par rapport au tableau de valeurs de filtre. Voici le pseudo-code avec la clé : `[v1, v2, v3]` et le filtre : `[a,b,c]`. Les valeurs de clé avec des types de données qui ne correspondent pas au type de données du filtre sont ignorées.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Les comparaisons de chaînes ne respectent pas toutes la casse.

> [!NOTE]
> Si le JSON de l’événement ne contient pas de clé de filtre avancé, le filtre est évalué comme **non correspondant** pour les opérateurs suivants : NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, NumberIn, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, StringIn.
> 
>Le filtre est évalué comme **correspondant** pour les opérateurs suivants : NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
L’opérateur IsNullOrUndefined prend la valeur true si la valeur de la clé est NULL ou non définie. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

Dans l’exemple suivant, key1 est manquant, donc l’opérateur prend la valeur true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

Dans l’exemple suivant, key1 est défini sur null, donc l’opérateur prend la valeur true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Si key1 a une autre valeur dans ces exemples, l’opérateur prend la valeur false. 

## <a name="isnotnull"></a>IsNotNull
L’opérateur IsNotNull prend la valeur true si la valeur de la clé n’est pas NULL ou n’est pas définie. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR et AND
Si vous spécifiez un seul filtre avec plusieurs valeurs, une opération **OU** est effectuée : la valeur du champ clé doit donc être une de ces valeurs. Voici un exemple :

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Si vous spécifiez plusieurs filtres différents, une opération **ET** est effectuée : chaque condition du filtre doit donc être satisfaite. Voici un exemple : 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
Pour les événements dans le **schéma des événements cloud**, utilisez les valeurs suivantes pour la clé : `eventid`, `source`, `eventtype`, `eventtypeversion` ou événement de données (comme `data.key1`). 

Vous pouvez également utiliser [des attributs de contexte d’extension dans CloudEvents 1.0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). Dans l’exemple suivant, `comexampleextension1` et `comexampleothervalue` sont des attributs de contexte d’extension. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Voici un exemple d’utilisation d’un attribut de contexte d’extension dans un filtre.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Limites

Le filtrage avancé présente les limites suivantes :

* 5 filtres avancés et 25 valeurs de filtre pour tous les filtres par abonnement Event Grid
* 512 caractères par valeur de type chaîne
* Cinq valeurs pour les opérateurs **dans** et **pas dans**
* Clés contenant le signe **`.` (point)** . Par exemple, `http://schemas.microsoft.com/claims/authnclassreference` ou `john.doe@contoso.com`. Actuellement, les caractères d’échappement ne sont pas pris en charge dans les clés. 

La même clé peut être utilisée dans plusieurs filtres.





## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le filtrage d’événements avec PowerShell et Azure CLI, consultez [Filtrer des événements pour Event Grid](how-to-filter-events.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
