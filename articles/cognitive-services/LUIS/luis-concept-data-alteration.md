---
title: Modification de données – LUIS
description: Découvrez comment les données peuvent être modifiées avant les prédictions de LUIS (Language Understanding)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292057"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Modifier des données d’énoncé avant ou pendant la prédiction
LUIS fournit des méthodes pour manipuler l’énoncé avant ou pendant la prédiction. Celles-ci incluent la [correction de l’orthographe](luis-tutorial-bing-spellcheck.md) et la résolution des problèmes de fuseau horaire pour l’entité [datetimeV2](luis-reference-prebuilt-datetimev2.md) prédéfinie.

## <a name="correct-spelling-errors-in-utterance"></a>Corriger les erreurs d’orthographe dans l’énoncé


### <a name="v3-runtime"></a>Runtime V3

Prétraitez le texte en procédant à la correction orthographique avant d’envoyer l’énoncé à LUIS. Utilisez les exemples d’énoncés avec la bonne orthographe pour vous assurer d’obtenir les bonnes prédictions.

Utilisez la [Vérification orthographique Bing](../bing-spell-check/overview.md) pour corriger le texte avant de l’envoyer à LUIS.

### <a name="prior-to-v3-runtime"></a>Avant le runtime v3

LUIS utilise [l’API Vérification orthographique Bing V7](../Bing-Spell-Check/overview.md) pour corriger les fautes d’orthographe dans l’énoncé. LUIS a besoin de la clé associée à ce service. Créez la clé, puis ajoutez la clé en tant que paramètre de chaîne de requête au [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356).

Le point de terminaison nécessite deux paramètres pour que les corrections orthographiques fonctionnent :

|Paramètre|Valeur|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Clé de point de terminaison d’[API Vérification orthographique v7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Lorsque [l’API Vérification orthographique Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) détecte une erreur, l’énoncé d’origine et l’énoncé corrigé sont retournés avec des prédictions à partir du point de terminaison.

#### <a name="v2-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

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

#### <a name="v2-prediction-endpoint-request"></a>[Demande de point de terminaison de prédiction V2](#tab/V2)

Ajouter 60 minutes :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Supprimer 60 minutes :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[Demande de point de terminaison de prédiction V3](#tab/V3)

Ajouter 60 minutes :

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Supprimer 60 minutes :

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Le code C# détermine la valeur correcte de timezoneOffset
Le code C# suivant utilise la méthode [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) de la classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) pour déterminer la valeur `timezoneOffset` correcte en fonction de l’heure du système :

```csharp
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
