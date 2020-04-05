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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203489"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte

L’API Analyse de texte accepte un texte non structuré et retourne une liste d’entités dont l’ambiguïté est levée avec des liens vers des informations supplémentaires sur le web. L’API prend en charge la reconnaissance d’entités nommées (NER) et la liaison d’entités.

### <a name="entity-linking"></a>Liaison d’entités

La liaison d’entités est la possibilité d’identifier une entité présente dans un texte et de lever l’ambiguïté sur son identité (par exemple, en déterminant si une occurrence du mot `Mars` fait référence à la planète ou au dieu romain de la guerre). Ce processus nécessite la présence d’une base de connaissances dans une langue appropriée pour lier les entités reconnues dans le texte. La liaison d’entités utilise [Wikipedia](https://www.wikipedia.org/) en tant que base de connaissances.


### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

La reconnaissance d’entités nommées (NER) est la capacité d’identifier différentes entités dans du texte et de les classer en classes ou types prédéfinis tels que : personne, lieu, événement, produit et organisation.  

Depuis la version 3, cette fonctionnalité de l’API Analyse de texte peut aussi identifier les types d’informations personnelles et sensibles tels que : numéro de téléphone, numéro de sécurité sociale, adresse e-mail et numéro de compte bancaire.  L’identification de ces entités peut vous aider à classer des documents sensibles et à rédiger des informations personnelles.

## <a name="named-entity-recognition-versions-and-features"></a>Fonctionnalités et versions de la reconnaissance d’entités nommées

L’API Analyse de texte offre deux versions de la reconnaissance d’entités nommées (v2 et v3). La version 3 (préversion publique) fournit des détails supplémentaires dans les entités qui peuvent être détectées et catégorisées.

| Fonctionnalité                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Méthodes pour les requêtes uniques et de lots                          | X      | X      |
| Reconnaissance d’entités de base dans plusieurs catégories              | X      | X      |
| Classification étendue pour les entités reconnues                 |        | X      |
| Séparez des points de terminaison distincts pour l’envoi de demandes de liaison d’entité et NER. |        | X      |
| Gestion des versions des modèles                                                |        | X      |

Pour plus d’informations, consultez [Prise en charge linguistique](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition).


#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>Types d’entités

La reconnaissance d’entité nommée v3 fournit une détection étendue sur plusieurs types. Actuellement, NER v3 peut reconnaître les catégories d’entités suivantes :

* Général
* Informations personnelles 

Pour obtenir la liste détaillée des entités et des langues prises en charge, consultez l’article [Types d’entités pris en charge par NER v3](../named-entity-types.md).

### <a name="request-endpoints"></a>Points de terminaison de requête

La reconnaissance d’entité nommée v3 utilise des points de terminaison distincts pour les demandes de liaison d’entité et NER. Utilisez un format d’URL ci-dessous en fonction de votre demande :

NER
* Entités générales - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Informations personnelles - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Gestion des versions des modèles

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="entity-types"></a>Types d’entités

> [!NOTE]
> La reconnaissance d’entités nommées (NER) version 2 ne prend en charge que les entités suivantes. NER v3 est en préversion publique et augmente considérablement le nombre et la profondeur des entités reconnues dans le texte.   

| Type  | Subtype | Exemple |
|:-----------   |:------------- |:---------|
| Personne        | N/A\*         | « Jeff », « Bill Gates »     |
| Emplacement      | N/A\*         | « Redmond, Washington », « Paris »  |
| Organisation  | N/A\*         | « Microsoft »   |
| Quantité      | Number        | « 6 », « six »     |
| Quantité      | Pourcentage    | « 50 % », « cinquante pour cent »|
| Quantité      | Ordinal       | « 2nd », « second »     |
| Quantité      | Age           | « 90 jours », « 30 ans »    |
| Quantité      | Devise      | « 10,99 $ »     |
| Quantité      | Dimension     | « 10 miles », « 40 cm »     |
| Quantité      | Température   | « 32 degrés »    |
| DateTime      | N/A\*         | « 6 h 30 le 4 février 2012 »      |
| DateTime      | Date          | « 2 mai 2017 », « 02/05/2017 »   |
| DateTime      | Temps          | « 8 h », « 8:00 »  |
| DateTime      | DateRange     | « Du 2 au 5 mai »    |
| DateTime      | TimeRange     | « De 18 à 19 h »     |
| DateTime      | Duration      | « 1 minute et 45 secondes »   |
| DateTime      | Définissez           | « Chaque mardi »     |
| URL           | N/A\*         | "https:\//www.bing.com"    |
| E-mail         | N/A\*         | "support@contoso.com" |
| Numéro de téléphone aux États-Unis  | N/A\*         | (Numéros de téléphone américains uniquement) « (312) 555-0176 » |
| Adresse IP    | N/A\*         | « 10.0.0.100 » |

\* Selon les entités entrées et extraites, certaines entités peuvent omettre le `SubType`.  Tous les types d’entités pris en charge répertoriés sont disponibles uniquement en anglais, chinois simplifié, français, allemand et espagnol.

### <a name="request-endpoints"></a>Points de terminaison de requête

La reconnaissance d’entités nommées v2 utilise un seul point de terminaison pour les demandes de liaison d’entités et NER :

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Envoie d’une requête d’API REST

### <a name="preparation"></a>Préparation

Vous devez disposer des documents JSON dans ce format : ID, texte, langue.

Chaque document ne doit pas dépasser 5 120 caractères et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande.

### <a name="structure-the-request"></a>Structurer la requête

Créez une requête POST. Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md) ou la **console de tests d’API** dans les liens suivants pour en structurer une rapidement et l’envoyer. 

> [!NOTE]
> Vous pouvez trouver la clé et le point de terminaison pour votre ressource Analyse de texte dans le portail Azure. Ces informations se trouvent dans la page **Démarrage rapide** de la ressource, sous **gestion des ressources**. 

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Informations de référence sur la reconnaissance d’entités nommées v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

La version 3 utilise des points de terminaison distincts pour les demandes de liaison d’entités et NER. Utilisez un format d’URL ci-dessous en fonction de votre demande :

NER
* Entités générales - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entités d’informations personnelles - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Informations de référence sur la reconnaissance d’entités nommées (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

La version 2 utilise le point de terminaison suivant pour les demandes de liaison d’entités et NER : 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Définissez un en-tête de requête pour inclure votre clé d’API Analyse de texte. Dans le corps de la demande, fournissez les documents JSON que vous avez préparés.

### <a name="example-ner-request"></a>Exemple de demande NER 

Voici un exemple de contenu que vous pouvez envoyer à l’API. Le format de la requête est le même pour les deux versions de l’API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
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

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="example-v3-responses"></a>Exemples de réponses NER v3

La version 3 fournit des points de terminaison distincts pour la liaison d’entités et NER. Les réponses pour les deux opérations figurent ci-dessous. 

#### <a name="example-ner-response"></a>Exemple de réponse NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Exemple de réponse de liaison d’entités

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Exemple de réponse NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail de liaison d'entités à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

* La reconnaissance d’entités nommées est disponible pour certaines langues dans deux versions.
* Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
* Les demandes POST sont envoyées à un ou plusieurs points de terminaison à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
* La sortie de réponse, qui se compose d’entités liées (y compris des scores de confiance, des décalages et des liens web, pour chaque ID de document), peut être utilisée dans n’importe quelle application.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Utilisation de la bibliothèque cliente Analyse de texte](../quickstarts/text-analytics-sdk.md)
* [Nouveautés](../whats-new.md)
