---
title: Modification de données – LUIS
titleSuffix: Azure Cognitive Services
description: Découvrez comment les données peuvent être modifiées avant les prédictions de LUIS (Language Understanding)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703174"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Modifier des données d’énoncé avant ou pendant la prédiction
LUIS fournit des méthodes pour manipuler l’énoncé avant ou pendant la prédiction. Celles-ci incluent la [correction de l’orthographe](luis-tutorial-bing-spellcheck.md) et la résolution des problèmes de fuseau horaire pour l’entité [datetimeV2](luis-reference-prebuilt-datetimev2.md) prédéfinie. 

## <a name="correct-spelling-errors-in-utterance"></a>Corriger les erreurs d’orthographe dans l’énoncé
LUIS utilise [l’API Vérification orthographique Bing V7](../Bing-Spell-Check/overview.md) pour corriger les fautes d’orthographe dans l’énoncé. LUIS a besoin de la clé associée à ce service. Créez la clé, puis ajoutez la clé en tant que paramètre de chaîne de requête au [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356). 

Vous pouvez également corriger les fautes d’orthographe dans le panneau **Test** en [entrant la clé](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). La clé est conservée en tant que variable de session dans le navigateur pour le panneau Test. Ajoutez la clé dans le panneau Test dans chaque session de navigateur où vous souhaitez corriger l’orthographe. 

L’utilisation de la clé dans le panneau Test et sur le point de terminaison est prise en compte dans le quota [d’utilisation de la clé](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/). LUIS implémente les limites de la Vérification orthographique Bing pour la longueur du texte. 

Le point de terminaison nécessite deux paramètres pour que les corrections orthographiques fonctionnent :

|Paramètre|Valeur|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Clé de point de terminaison d’[API Vérification orthographique v7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Lorsque [l’API Vérification orthographique Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) détecte une erreur, l’énoncé d’origine et l’énoncé corrigé sont retournés avec des prédictions à partir du point de terminaison.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>Liste des mots autorisés
L’API Vérification orthographique Bing utilisée dans LUIS ne prend pas en charge la liste des mots à ignorer durant les modifications résultant de la vérification orthographique. Si vous avez besoin d’autoriser une liste de mots ou d’acronymes, traitez l’énoncé dans l’application cliente avant de l’envoyer à LUIS pour la prédiction d’intention.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Changer le fuseau horaire de l’entité datetimeV2 prédéfinie
Quans une application LUIS utilise l’entité [datetimeV2](luis-reference-prebuilt-datetimev2.md) prédéfinie, une valeur datetime peut être retournée dans la réponse de prédiction. Le fuseau horaire de la requête est utilisé pour déterminer la valeur datetime correcte à retourner. Si la requête provient d’un bot ou d’une autre application centralisée avant d’accéder à LUIS, corrigez le fuseau horaire que LUIS utilise. 

### <a name="endpoint-querystring-parameter"></a>Paramètre querystring de point de terminaison
Le fuseau horaire est corrigé par l’ajout du fuseau horaire de l’utilisateur au [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356) à l’aide du paramètre `timezoneOffset`. La valeur de `timezoneOffset` doit être un nombre positif ou négatif, en minutes, pour modifier l’heure.  

|Paramètre|Valeur|
|--|--|
|`timezoneOffset`|nombre positif ou négatif, en minutes|

### <a name="daylight-savings-example"></a>Exemple d’économies de l’heure d’été
Si vous voulez que l’entité datetimeV2 prédéfinie retournée soit ajustée à l’heure d’été, vous devez utiliser le paramètre querystring `timezoneOffset` avec une valeur +/- en minutes pour la requête du [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356).

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Demande de point de terminaison de prédiction V2](#tab/V2)

Ajouter 60 minutes : 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Supprimer 60 minutes : 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Demande de point de terminaison de prédiction V3](#tab/V3)

Ajouter 60 minutes :

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Supprimer 60 minutes : 

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Apprenez-en davantage sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Le code C# détermine la valeur correcte de timezoneOffset
Le code C# suivant utilise la méthode [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) de la classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) pour déterminer la valeur `timezoneOffset` correcte en fonction de l’heure du système :

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Corriger les erreurs d’orthographe avec ce tutoriel](luis-tutorial-bing-spellcheck.md)
