---
title: Appeler l’API Text Analytics
titleSuffix: Azure Cognitive Services
description: Cet article explique comment appeler l’API REST Analyse de texte Azure Cognitive Services et Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993353"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Comment appeler l’API REST Analyse de texte

Dans cet article, nous utilisons l’API REST Analyse de texte et [Postman](https://www.postman.com/downloads/) pour présenter des concepts clés. L’API fournit plusieurs points de terminaison synchrones et asynchrones pour l’utilisation des fonctionnalités du service. 

## <a name="using-the-api-asynchronously"></a>Utilisation de l’API de manière asynchrone

Depuis la version v3.1-preview.3, l’API Analyse de texte fournit deux points de terminaison asynchrones : 

* Le point de terminaison `/analyze` pour l’Analyse de texte vous permet d’analyser le même jeu de documents texte avec plusieurs fonctionnalités d’analyse de texte en un seul appel d’API. Auparavant, pour utiliser plusieurs fonctionnalités, vous deviez effectuer des appels d’API distincts pour chaque opération. Tenez compte de cette fonctionnalité lorsque vous devez analyser de grands ensembles de documents avec plusieurs fonctionnalités d’Analyse de texte.

* Le point de terminaison `/health` pour l’Analyse de texte en lien avec la santé, qui peut extraire et étiqueter des informations médicales pertinentes à partir de documents cliniques.  

Consultez le tableau ci-dessous pour savoir quelles fonctionnalités vous pouvez utiliser de manière asynchrone. Notez que vous ne pouvez appeler que quelques fonctionnalités à partir du point de terminaison `/analyze`. 

| Fonctionnalité | Synchrone | Asynchrone |
|--|--|--|
| Détection de la langue | ✔ |  |
| analyse de sentiments | ✔ |  |
| Exploration des opinions | ✔ |  |
| Extraction d’expressions clés | ✔ | ✔* |
| Reconnaissance d’entité nommée (y compris PII et PHI) | ✔ | ✔* |
| Analyse de texte pour la santé (conteneur) | ✔ |  |
| Analyse de texte pour la santé (API) |  | ✔  |

`*` - Appelé de façon asynchrone via le point de terminaison `/analyze`.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Prérequis


> [!NOTE]
> Si vous souhaitez utiliser les points de terminaison `/analyze` ou `/health`, vous aurez besoin d’une ressource d’Analyse de texte utilisant un [niveau de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S).

1.  Tout d’abord, accédez au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) et créez une ressource d’Analyse de texte si vous n’en avez pas encore. Si vous souhaitez utiliser les points de terminaison `/analyze` ou `/health`, choisissez le niveau de tarification Standard (S).

2.  Sélectionnez la région que votre point de terminaison doit utiliser.

3.  Créez la ressource d’Analyse de texte et accédez au panneau « Clés et point de terminaison » dans la partie gauche de la page. Copiez la clé à utiliser ultérieurement lors de l’appel des API. Vous l’ajouterez plus tard en tant que valeur pour l’en-tête `Ocp-Apim-Subscription-Key`.


<a name="json-schema"></a>

## <a name="api-request-format"></a>Format de demande API

#### <a name="synchronous"></a>[Synchrone](#tab/synchronous)

Le format des demandes d’API est le même pour toutes les opérations synchrones. Les documents sont envoyés dans un objet JSON sous forme de texte brut non structuré. XML n’est pas pris en charge. Le schéma JSON se compose des éléments décrits ci-dessous.

| Élément | Valeurs valides | Requis ? | Usage |
|---------|--------------|-----------|-------|
|`id` |Les données dont de type chaîne mais, dans la pratique, les ID de document tendent à être des entiers. | Obligatoire | Le système utilise les ID que vous fournissez pour structurer la sortie. Des codes langue, phrases clés et scores de sentiment sont générés pour chaque ID dans la demande.|
|`text` | Texte brut non structuré, jusqu’à 5 120 caractères. | Obligatoire | Pour la détection de la langue, le texte peut être exprimé dans toute langue. Pour l’analyse des sentiments, l’extraction de phrases clés et l’identification d’entité, le texte doit être dans une [langue prise en charge](../language-support.md). |
|`language` | Code [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caractères d’une [langue prise en charge](../language-support.md) | Variable | Obligatoire pour l’analyse des sentiments, l’extraction de phrases clés et la liaison d’entité. Facultatif pour la détection de la langue. Son omission ne génère aucune erreur, mais affaiblit l’analyse. Le code de langue doit correspondre au `text` que vous fournissez. |

Voici un exemple de demande API pour les points de terminaison d’Analyse de texte synchrone. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Analyser](#tab/analyze)

> [!NOTE]
> La dernière préversion de la bibliothèque de client d’Analyse de texte vous permet d’appeler des opérations d’Analyse asynchrone en utilisant un objet client. Vous trouverez des exemples sur GitHub :
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Le point de terminaison `/analyze` vous permet de choisir les fonctionnalités d’Analyse de texte prises en charge que vous souhaitez utiliser dans un appel d’API. Ce point de terminaison prend actuellement en charge les aspects suivants :

* à l’extraction de phrases clés 
* Reconnaissance d’entité nommée (y compris PII et PHI)

| Élément | Valeurs valides | Requis ? | Usage |
|---------|--------------|-----------|-------|
|`displayName` | String | Facultatif | Utilisé comme nom d’affichage pour l’identificateur unique du travail.|
|`analysisInput` | Inclut le champ `documents` en dessous | Obligatoire | Contient les informations relatives aux documents que vous souhaitez envoyer. |
|`documents` | Inclut les champs `id` et `text` en dessous | Obligatoire | Contient des informations pour chaque document envoyé, ainsi que le texte brut du document. |
|`id` | String | Obligatoire | Les ID que vous fournissez sont utilisés pour structurer la sortie. |
|`text` | Texte brut non structuré, jusqu’à 125 000 caractères. | Obligatoire | Doit être en anglais, qui est la seule langue actuellement prise en charge. |
|`tasks` | Inclut les fonctionnalités d’Analyse de texte suivantes : `entityRecognitionTasks`, `keyPhraseExtractionTasks` ou `entityRecognitionPiiTasks`. | Obligatoire | Une ou plusieurs fonctionnalités d’Analyse de texte que vous souhaitez utiliser. Notez que `entityRecognitionPiiTasks` a un paramètre facultatif `domain` qui peut être défini sur `pii` ou `phi`. Si la valeur n’est pas spécifiée, la valeur système par défaut est `pii`. |
|`parameters` | Inclut les champs `model-version` et `stringIndexType` en dessous | Obligatoire | Ce champ est inclus dans les tâches de fonctionnalités au-dessus que vous choisissez. Ils contiennent des informations sur la version du modèle que vous souhaitez utiliser et sur le type d’index. |
|`model-version` | String | Obligatoire | Spécifiez la version du modèle appelée que vous souhaitez utiliser.  |
|`stringIndexType` | String | Obligatoire | Spécifiez le décodeur de texte correspondant à votre environnement de programmation.  Les types pris en charge sont `textElement_v8` (par défaut), `unicodeCodePoint` et `utf16CodeUnit`. Pour plus d’informations, consultez l’article sur les [décalages de texte](../concepts/text-offsets.md#offsets-in-api-version-31-preview).  |
|`domain` | String | Facultatif | S’applique uniquement en tant que paramètre à la tâche `entityRecognitionPiiTasks` et peut être défini sur `pii` ou `phi`. Si la valeur n’est pas spécifiée, par défaut, il s’agit de `pii`.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Analyse de texte pour la santé](#tab/health)

Le format des demandes d’API adressées à l’Analyse de texte pour l’API hébergée de santé est le même que pour son conteneur. Les documents sont envoyés dans un objet JSON sous forme de texte brut non structuré. XML n’est pas pris en charge. Le schéma JSON se compose des éléments décrits ci-dessous.  Complétez et envoyez le [formulaire de demande Cognitive Services](https://aka.ms/csgate) pour demander l’accès à la préversion publique d’Analyse de texte pour la santé. Vous ne serez pas facturé pour l’utilisation de l’Analyse de texte pour la santé. 

| Élément | Valeurs valides | Requis ? | Usage |
|---------|--------------|-----------|-------|
|`id` |Les données dont de type chaîne mais, dans la pratique, les ID de document tendent à être des entiers. | Obligatoire | Le système utilise les ID que vous fournissez pour structurer la sortie. |
|`text` | Texte brut non structuré, jusqu’à 5 120 caractères. | Obligatoire | Notez que seul du texte en anglais est actuellement pris en charge. |
|`language` | Code [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caractères d’une [langue prise en charge](../language-support.md) | Obligatoire | Seule la langue `en` est actuellement prise en charge. |

Voici un exemple de demande API pour l’Analyse de texte pour des points de terminaison de santé. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Pour plus d’informations sur les limites de débits et de taille pour l’envoi de données à l’API Analyse de texte, consultez l’article [Limites de données et de débit](../concepts/data-limits.md) .


## <a name="set-up-a-request"></a>Configurer une demande 

Dans Postman (ou un autre outil de test d’API web), ajoutez le point de terminaison pour la fonctionnalité que vous souhaitez utiliser. Utilisez le tableau en dessous pour rechercher le format de point de terminaison approprié, puis remplacez `<your-text-analytics-resource>` par le point de terminaison de votre ressource. Par exemple :

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchrone](#tab/synchronous)

| Fonctionnalité | Type de demande | Points de terminaison de ressource |
|--|--|--|
| Détection de la langue | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| analyse de sentiments | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Exploration des opinions | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extraction d’expressions clés | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Reconnaissance d’entité nommée – Général | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Reconnaissance d’entité nommée – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Reconnaissance d’entité nommée – PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Analyser](#tab/analyze)

| Fonctionnalité | Type de demande | Points de terminaison de ressource |
|--|--|--|
| Envoyer un travail d’analyse | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Obtenir l’état et les résultats d’une analyse | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Analyse de texte pour la santé](#tab/health)

| Fonctionnalité | Type de demande | Points de terminaison de ressource |
|--|--|--|
| Envoyer une Analyse de texte pour un travail sur la santé  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Obtenir l’état et les résultats d’un travail | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Annuler le travail | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Une fois que vous avez votre point de terminaison, dans Postman (ou un autre outil de test d’API web) :

1. Choisissez le type de demande pour la fonctionnalité que vous souhaitez utiliser.
2. Collez le point de terminaison de l’opération appropriée souhaitée à partir du tableau ci-dessus.
3. Définissez les trois en-têtes de demande :

   + `Ocp-Apim-Subscription-Key` : votre clé d’accès, obtenue à partir du portail Azure
   + `Content-Type` : application/json
   + `Accept` : application/json

    Si vous utilisez Postman, votre demande doit ressembler à la capture d’écran suivante, en supposant un point de terminaison `/keyPhrases`.
    
    ![Capture d’écran de demande avec point de terminaison et en-têtes](../media/postman-request-keyphrase-1.png)
    
4. Choisissez **brut** pour le format du **Corps**
    
    ![Capture d’écran de demande avec paramètres du corps](../media/postman-request-body-raw.png)

5. Collez des documents JSON dans un format valide. Utilisez les exemples de la section **format de demande API** ci-dessus et, pour plus d’informations et d’exemples, consultez les rubriques ci-dessous :

      + [Détection de la langue](text-analytics-how-to-language-detection.md)
      + [Extraction de phrases clés](text-analytics-how-to-keyword-extraction.md)
      + [Analyse des sentiments](text-analytics-how-to-sentiment-analysis.md)
      + [Reconnaissance d’entités](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Envoyer la demande

Envoyez la demande API. Si vous avez appelé un point de terminaison synchrone, la réponse s’affiche immédiatement, sous la forme d’un document JSON unique, avec un élément pour chaque ID de document fourni dans la demande.

Si vous avez appelé les points de terminaison asynchrones `/analyze` ou `/health`, vérifiez que vous avez reçu un code de réponse 202. Vous devrez obtenir la réponse pour afficher les résultats :

1. Dans la réponse de l’API, recherchez `Operation-Location` dans l’en-tête, qui identifie le travail que vous avez envoyé à l’API. 
2. Créez une demande GET pour le point de terminaison que vous avez utilisé. Consultez le [tableau ci-dessus](#set-up-a-request) pour le format du point de terminaison, ainsi que la [documentation de référence de l’API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Par exemple :

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Ajoutez `Operation-Location` à la demande.

4. La réponse sera un document JSON unique, avec un élément pour chaque ID de document fourni dans la demande.

## <a name="example-api-responses"></a>Exemples de réponses de l’API
 
# <a name="synchronous"></a>[Synchrone](#tab/synchronous)

Les réponses de point de terminaison synchrone varient en fonction du point de terminaison que vous utilisez. Pour obtenir des exemples de réponses, consultez les articles suivants.

+ [Détection de la langue](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extraction de phrases clés](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analyse des sentiments](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Reconnaissance d’entités](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Analyser](#tab/analyze)

En cas de réussite, la demande GET adressée au point de terminaison `/analyze` retourne un objet contenant les tâches affectées. Par exemple, `keyPhraseExtractionTasks`. Ces tâches contiennent l’objet réponse de la fonctionnalité d’Analyse de texte appropriée. Consultez les articles suivants pour plus d’informations.

+ [Extraction de phrases clés](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Reconnaissance d’entités](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Analyse de texte pour la santé](#tab/health)

Pour plus d’informations sur l’Analyse de texte de réponse de l’API asynchrone de santé, consultez l’article suivant :

+ [Analyse de texte pour la santé](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712)
* [Utilisation de la bibliothèque cliente Analyse de texte](../quickstarts/text-analytics-sdk.md)
* [Nouveautés](../whats-new.md)
