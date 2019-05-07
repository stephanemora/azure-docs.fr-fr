---
title: V2 pour la migration de l’API de V3
titleSuffix: Azure Cognitive Services
description: Le point de terminaison version 3 API ont été modifiés. Utilisez ce guide pour comprendre comment migrer vers les API du point de terminaison de version 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d0b5537f31edf330b54b91ddf7268338df0c0a6b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148272"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>Aperçu : Migrer vers la version d’API pour les applications LUIS 3.x

Le point de terminaison de prédiction de requête API ont été modifiés. Utilisez ce guide pour comprendre comment migrer vers les API du point de terminaison de version 3. 

Cette API V3 fournit les nouvelles fonctionnalités suivantes, qui incluent les importantes modifications de requête et/ou de réponse JSON : 

* [Entités externes](#external-entities-passed-in-at-prediction-time)
* [Listes dynamiques](#dynamic-lists-passed-in-at-prediction-time)
* [Modifications JSON prédéfini d’entité](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Le point de terminaison de prédiction de requête [demande](#request-changes) et [réponse](#response-changes) apportez des modifications importantes pour prendre en charge les nouvelles fonctionnalités répertoriées ci-dessus, y compris les éléments suivants :

* [Modifications de l’objet réponse](#top-level-json-changes)
* [Références de nom de rôle entité au lieu du nom de l’entité](#entity-role-name-instead-of-entity-name)
* [Propriétés pour marquer des entités dans énoncés](#marking-placement-of-entities-in-utterances)

Les fonctionnalités suivantes de LUIS sont **ne pas pris en charge** dans l’API V3 :

* V7 de vérification orthographique Bing

[Documentation de référence](https://aka.ms/luis-api-v3) est disponible pour V3.

## <a name="prebuilt-domains-with-new-models-and-language-coverage"></a>Domaines prédéfinis avec nouveaux modèles et de langues

Examinez le [liste les API V3 de domaines prédéfinis](luis-reference-prebuilt-domains.md). Ces domaines sont plus complètes, à la fois dans le modèle et la couverture de langage. 

## <a name="prebuilt-entities-with-new-json"></a>Avec le nouveau modèle JSON des entités prédéfinies

Incluent les modifications d’objet de réponse V3 [des entités prédéfinies](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Demander des changements 

### <a name="query-string-parameters"></a>Paramètres de chaîne de requête

L’API V3 a des paramètres de chaîne de requête différents.

|Nom de paramètre|Type|Version|Objectif|
|--|--|--|--|
|`query`|string|V3 uniquement|**Dans V2**, l’énoncé à prédire est dans le `q` paramètre. <br><br>**Dans V3**, la fonctionnalité est passée dans le `query` paramètre.|
|`show-all-intents`|booléenne|V3 uniquement|Retourner tous les intentions avec le score correspondant dans le **prediction.intents** objet. Intentions sont retournées en tant qu’objets dans un parent `intents` objet. Cela permet un accès par programme sans avoir à rechercher l’intention dans un tableau : `prediction.intents.give`. Dans V2, ceux-ci ont été retournés dans un tableau. |
|`verbose`|booléenne|V2 & V3|**Dans V2**, quand la valeur à true, tout prédites intentions ont été retournées. Si vous avez besoin tout prédites intentions, utilisez le paramètre V3 de `show-all-intents`.<br><br>**Dans V3**, ce paramètre fournit des entités uniquement les informations des métadonnées de prédiction de l’entité.  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>La prédiction de requête JSON de corps pour le `POST` demande

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

## <a name="response-changes"></a>Modifications de la réponse

La réponse de requête JSON modifiée pour autoriser un accès par programme supérieure aux données les plus fréquemment utilisées. 

### <a name="top-level-json-changes"></a>Modifications JSON de niveau supérieures

Sont les premières propriétés JSON pour V2, lorsque `verbose` est définie sur true, ce qui renvoie tous les intentions et leurs scores dans le `intents` propriété :

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Les principales propriétés JSON pour V3 sont :

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

Le `intents` objet est une liste non triée. Ne supposez pas que le premier enfant dans le `intents` correspond à la `topIntent`. Au lieu de cela, utilisez le `topIntent` valeur à rechercher le score :

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Autoriser les modifications de schéma de réponse JSON :

* Effacer la distinction entre énoncé d’origine, `query`et retourné de prédiction, `prediction`.
* Accès plus facile par programme aux données prédites. Au lieu de l’énumération d’un tableau dans V2, vous pouvez accéder à valeurs par **nommé** pour les intentions et entités. Pour les rôles de l’entité prévue, le nom de rôle est retourné, car il est unique dans toute l’application.
* Types de données déterminée, sont respectées. Valeurs numériques sont retournées ne sont plus sous forme de chaînes.
* Distinction entre les premières informations de prédiction de priorité et des métadonnées supplémentaires retournées dans le `$instance` objet. 

### <a name="access-instance-for-entity-metadata"></a>Accès `$instance` pour les métadonnées d’entité

Si vous avez besoin des métadonnées d’entité, la chaîne de requête doit utiliser le `verbose=true` indicateur et la réponse contient les métadonnées dans le `$instance` objet. Exemples sont présentés dans les réponses JSON dans les sections suivantes.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Chaque entité prévue est représentée sous forme de tableau

Le `prediction.entities.<entity-name>` objet contient un tableau, car chaque entité peut être prédite plusieurs fois dans l’énoncé. 

### <a name="list-entity-prediction-changes"></a>Liste entité prédiction change

Le code JSON pour une prédiction d’entité de liste a changé pour être un tableau de tableaux :

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Chaque tableau intérieur correspond au texte à l’intérieur de l’énoncé. L’objet est un tableau, car le même texte peut apparaître dans plusieurs sous-liste d’une entité de la liste. 

Lors du mappage entre le `entities` de l’objet à le `$instance` de l’objet, l’ordre des objets est conservé pour les prédictions d’entité de liste.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nom de rôle d’entité au lieu du nom de l’entité 

Dans V2, la `entities` tableau retourné toutes les entités prédites avec le nom de l’entité en cours de l’identificateur unique. Dans V3, si l’entité utilise des rôles et la prédiction est pour un rôle d’entité, l’identificateur principal est le nom du rôle. Cela est possible, car les noms de rôles d’entité doivent être uniques dans l’application entière, y compris d’autres noms de modèle (intent, entity).

Dans l’exemple suivant : un énoncé qui inclut le texte, de prendre en compte `Yellow Bird Lane`. Ce texte est prédite en tant que personnalisé `Location` rôle de l’entité de `Destination`.

|Texte de l’énoncé|Nom de l’entité|Nom de rôle|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Dans V2, l’entité est identifiée par le _nom de l’entité_ avec le rôle en tant que propriété de l’objet :

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Dans V3, l’entité est référencée par le _rôle entité_, si la prédiction est pour le rôle :

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Dans V3, le même résultat avec la `verbose` indicateur pour retourner les métadonnées d’entité :

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Les entités externes passée au moment de la prédiction

Les entités externes permettent à votre application LUIS pour identifier et étiqueter des entités pendant l’exécution, ce qui peut être utilisée en tant que fonctionnalités aux entités existantes. Cela vous permet d’utiliser vos propres extracteurs entité distinct et personnalisé avant d’envoyer des requêtes à votre point de terminaison de prédiction. Cette opération est effectuée sur le point de terminaison de prédiction de requête, vous n’avez pas besoin reformer et publier votre modèle.

L’application cliente fournit son propre extracteur d’entité en gérant l’entité mise en correspondance et déterminer l’emplacement au sein de l’énoncé de cette entité de mise en correspondance et envoyer ces informations avec la demande. 

Les entités externes sont le mécanisme permettant d’étendre n’importe quel type d’entité tout en étant toujours utilisé comme signaux à d’autres modèles, tels que les rôles, composite et d’autres.

Cela est utile pour une entité qui comporte des données uniquement lors de l’exécution de prédiction de requête. Exemples de ce type de données évoluent en permanence les données ou spécifiques par utilisateur. Vous pouvez étendre une entité contact LUIS avec des informations externes à partir de la liste des contacts d’un utilisateur. 

### <a name="entity-already-exists-in-app"></a>Entité existe déjà dans l’application

La valeur de `entityName` pour l’entité externe, transmise dans la requête de point de terminaison corps POST, doit déjà exister dans l’application formée et publiée au moment de la demande est effectuée. Peu importe le type d’entité, tous les types sont pris en charge.

### <a name="first-turn-in-conversation"></a>Tout d’abord activer dans la conversation

Prenez en compte une première énoncé dans une conversation de robot de conversation où un utilisateur entre les informations suivantes incomplètes :

`Send Hazem a new message`

La demande de l’agent conversationnel pour LUIS permettre passer des informations dans le corps POST sur `Hazem` afin qu’il est directement mis en correspondance comme l’un des contacts de l’utilisateur.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La réponse de prédiction inclut cette entité externe, avec toutes les autres prédites entités, car il est défini dans la demande.  

### <a name="second-turn-in-conversation"></a>Deuxième tour de conversation

L’énoncé utilisateur suivant dans l’agent conversationnel utilise un terme plus vague :

`Send him a calendar reminder for the party.`

Dans l’énoncé précédente, l’énoncé utilise `him` comme une référence à `Hazem`. Le bot conversationnel de conversation, dans le corps POST, peut mapper `him` sur la valeur de l’entité extraite à partir de la première énoncé, `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

La réponse de prédiction inclut cette entité externe, avec toutes les autres prédites entités, car il est défini dans la demande.  

#### <a name="resolution"></a>Résolution :

Le _facultatif_ `resolution` retourne de propriété dans la réponse de prédiction, ce qui vous permet de passer dans les métadonnées associées à l’entité externe, puis recevoir reviennent dans la réponse. 

L’objectif principal consiste à étendre des entités prédéfinies, mais il n’est pas limité à ce type d’entité. 

Le `resolution` propriété peut être un nombre, une chaîne, un objet ou un tableau :

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]


## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Les listes dynamiques passée au moment de la prédiction

Les listes dynamiques permettent d’étendre une entité formé et publié la liste existante, déjà dans l’application LUIS. 

Utilisez cette fonctionnalité lorsque vos valeurs d’entité de liste doivent être modifiées périodiquement. Cette fonctionnalité vous permet d’étendre une entité de la liste déjà formé et publié :

* Au moment de la demande de point de terminaison de prédiction de requête.
* Pour une demande unique.

L’entité de la liste peut être vide dans l’application LUIS, mais il doit exister. L’entité de la liste dans l’application LUIS n’est pas modifiée, mais la capacité de prédiction au point de terminaison est étendue pour inclure des listes jusqu'à 2 avec plus de 1 000 éléments.

### <a name="dynamic-list-json-request-body"></a>Corps de requête JSON de liste dynamique

Envoyer dans le corps JSON suivant pour ajouter une nouvelle sous-liste avec des synonymes à la liste et prévoir de l’entité de la liste pour le texte, `LUIS`, avec le `POST` requête de prédiction :

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

La réponse de prédiction inclut cette entité de la liste, avec toutes les autres prédites entités, car il est défini dans la demande. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset renommé en datetimeReference

**Dans V2**, le `timezoneOffset` [paramètre](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) est envoyé dans la requête de prédiction comme paramètre de chaîne de requête, peu importe si la demande est envoyée en tant qu’une requête GET ou POST. 

**Dans V3**, la même fonctionnalité est fournie avec le paramètre de corps POST, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Marquage de sélection élective d’entités dans énoncés

**Dans V2**, une entité a été marquée dans un énoncé avec le `startIndex` et `endIndex`. 

**Dans V3**, l’entité est marquée avec `startIndex` et `entityLength`.


## <a name="next-steps"></a>Étapes suivantes

Utilisez la documentation de l’API de V3 pour mettre à jour REST existant appelle à LUIS [point de terminaison](https://aka.ms/luis-api-v3) API. 