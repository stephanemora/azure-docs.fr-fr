---
title: Utiliser une reconnaissance d’entité avec l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Découvrez comment identifier et désambiguïser l’identité d’une entité trouvée dans le texte avec l’API REST Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/09/2020
ms.author: aahi
ms.openlocfilehash: f37828d5561ef382f572c1fdd4917a71f8a00407
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930167"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte

L’API Analyse de texte accepte un texte non structuré et retourne une liste d’entités dont l’ambiguïté est levée avec des liens vers des informations supplémentaires sur le web. L’API prend en charge la reconnaissance d’entités nommées (NER) et la liaison d’entités.

### <a name="entity-linking"></a>Liaison d’entités

La liaison d’entités est la possibilité d’identifier une entité présente dans un texte et de lever l’ambiguïté sur son identité (par exemple, en déterminant si une occurrence du mot « Mars » fait référence à la planète ou au dieu romain de la guerre). Ce processus nécessite la présence d’une base de connaissances dans une langue appropriée pour lier les entités reconnues dans le texte. La liaison d’entités utilise [Wikipedia](https://www.wikipedia.org/) en tant que base de connaissances.


### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

La reconnaissance d’entités nommées (NER) est la capacité d’identifier différentes entités dans du texte et de les classer en classes ou types prédéfinis tels que : personne, lieu, événement, produit et organisation.  

## <a name="named-entity-recognition-versions-and-features"></a>Fonctionnalités et versions de la reconnaissance d’entités nommées

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Fonctionnalité                                                         | NER v3.0 | NER v3.1-preview.2 |
|-----------------------------------------------------------------|--------|----------|
| Méthodes pour les requêtes uniques et de lots                          | X      | X        |
| Reconnaissance d’entité étendue dans plusieurs catégories           | X      | X        |
| Séparez des points de terminaison distincts pour l’envoi de demandes de liaison d’entité et NER. | X      | X        |
| Reconnaissance des entités d’informations personnelles (`PII`) et médicales (`PHI`)        |        | X        |

Pour plus d’informations, consultez [Prise en charge linguistique](../language-support.md).

### <a name="entity-types"></a>Types d’entités

La reconnaissance d’entité nommée v3 fournit une détection étendue sur plusieurs types. Actuellement, NER v3.0 peut reconnaître des entités dans la [catégorie d’entité générale](../named-entity-types.md).

La Reconnaissance d’entité nommée v3.1-preview.2 comprend les capacités de détection de v3.0 et la possibilité de détecter les informations personnelles (`PII`) à l’aide du point de terminaison `v3.1-preview.2/entities/recognition/pii`. Vous pouvez utiliser le paramètre facultatif `domain=phi` pour détecter les informations médicales confidentielles (`PHI`). Pour plus d’informations, consultez l’article sur les [catégories d’entité](../named-entity-types.md) et la section sur les [points de terminaison de demande](#request-endpoints) ci-dessous.


## <a name="sending-a-rest-api-request"></a>Envoie d’une requête d’API REST

### <a name="preparation"></a>Préparation

Vous devez disposer des documents JSON dans ce format : ID, texte, langue.

Chaque document ne doit pas dépasser 5 120 caractères et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande.

### <a name="structure-the-request"></a>Structurer la requête

Créez une requête POST. Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md) ou la **console de tests d’API** dans les liens suivants pour en structurer une rapidement et l’envoyer. 

> [!NOTE]
> Vous pouvez trouver la clé et le point de terminaison pour votre ressource Analyse de texte dans le portail Azure. Ces informations se trouvent dans la page **Démarrage rapide** de la ressource, sous **gestion des ressources**. 


### <a name="request-endpoints"></a>Points de terminaison de requête

#### <a name="version-31-preview2"></a>[Version 3.1-preview.2](#tab/version-3-preview)

La reconnaissance d’entité nommée `v3.1-preview.2` utilise des points de terminaison distincts pour les demandes NER et de liaison d’entités. Utilisez un format d’URL ci-dessous en fonction de votre demande :

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/linking`

[Référence Reconnaissance d’entité nommée version 3.1-preview.2 pour `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

NER
* Entités générales - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/general`

[Référence Reconnaissance d’entité nommée version 3.1-preview.2 pour `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)

* Informations personnelles (`PII`) - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii`

Vous pouvez aussi utiliser le paramètre facultatif `domain=phi` pour détecter les informations médicales (`PHI`) dans le texte. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi`

Notez l’ajout de la propriété `redactedText` dans la réponse JSON qui contient le texte d’entrée modifié où les entités PII détectées sont remplacées par un * pour chaque caractère des entités.

[Référence Reconnaissance d’entité nommée version 3.1-preview.2 pour `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

La reconnaissance d’entité nommée v3 utilise des points de terminaison distincts pour les demandes de liaison d’entité et NER. Utilisez un format d’URL ci-dessous en fonction de votre demande :

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Référence Reconnaissance d’entité nommée version 3.0 pour `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Référence Reconnaissance d’entité nommée version 3.0 pour `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Définissez un en-tête de requête pour inclure votre clé d’API Analyse de texte. Dans le corps de la demande, fournissez les documents JSON que vous avez préparés.

### <a name="example-ner-request"></a>Exemple de demande NER 

Voici un exemple de contenu que vous pouvez envoyer à l’API. Le format de la requête est le même pour les deux versions de l’API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>Publier la requête

L’analyse est effectuée à la réception de la demande. Consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et seconde.

L’API Analyse de texte est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

## <a name="view-results"></a>Afficher les résultats

Toutes les demandes POST retournent une réponse au format JSON avec les ID et les propriétés des entités détectées.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données. En raison de la prise en charge multilingue et des émojis, la réponse peut contenir des décalages de texte. Pour plus d’informations, consultez le [guide pratique pour traiter les décalages de texte](../concepts/text-offsets.md).

### <a name="example-responses"></a>Exemples de réponses

La version 3 fournit des points de terminaison distincts pour Reconnaissance d’entité nommée, les informations d’identification personnelle et la liaison d’entités. Les réponses pour les deux opérations figurent ci-dessous. 

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-preview)

Exemple de réponse PII :
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Exemple de réponse de liaison d’entités :

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

Exemple de réponse NER générale :
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail de liaison d'entités à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

* Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
* Les demandes POST sont envoyées à un ou plusieurs points de terminaison à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
* La sortie de réponse, qui se compose d’entités liées (y compris des scores de confiance, des décalages et des liens web, pour chaque ID de document), peut être utilisée dans n’importe quelle application.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Utilisation de la bibliothèque cliente Analyse de texte](../quickstarts/text-analytics-sdk.md)
* [Nouveautés](../whats-new.md)
