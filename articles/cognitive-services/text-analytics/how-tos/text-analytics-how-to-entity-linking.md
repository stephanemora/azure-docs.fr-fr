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
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326641"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte

L’[API Reconnaissance d’entités nommées](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) accepte un texte non structuré et, pour chaque document JSON, retourne la liste des entités dont l’ambiguïté est levée avec des liens vers des informations supplémentaires sur le web (Wikipedia et Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Liaison d’entités et reconnaissance d’entité nommée

Le point de terminaison `entities` d’Analyse de texte prend en charge la reconnaissance d’entités nommées (NER) et la liaison d’entités.

### <a name="entity-linking"></a>Liaison d’entités
La liaison d’entités est la possibilité d’identifier une entité présente dans un texte et de lever l’ambiguïté sur son identité (par exemple, en déterminant si « Mars » fait référence à la planète ou au dieu romain de la guerre). Ce processus nécessite la présence d’une base de connaissances à laquelle les entités reconnues sont liées. Wikipédia est utilisé en tant que base de connaissances pour le point de terminaison `entities` d’Analyse de texte.

### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)
La reconnaissance d’entité nommée (NER) est la capacité d’identifier différentes entités dans du texte et de les classer en classes ou types prédéfinis. 

## <a name="named-entity-recognition-v3-public-preview"></a>Préversion publique de la reconnaissance d’entité nommée v3

La prochaine version de la reconnaissance d’entité nommée est désormais disponible en préversion publique. Elle offre des mises à jour des liaisons d’entités et une reconnaissance d’entité nommée. Essayez-la en utilisant la [console de test d’API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

:::row:::
    :::column span="":::
        **Fonctionnalité**
    :::column-end:::
    ::: column span="":::
        **Description** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Types et sous-types d’entité développés
    :::column-end:::
    :::column span="":::
     Classification et détection étendues pour plusieurs types d’entités nommés.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Séparer les points de terminaison de requête 
    :::column-end:::
    :::column span="":::
        Séparez des points de terminaison distincts pour l’envoi de demandes de liaison d’entité et NER.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        Paramètre `model-version`
    :::column-end:::
    :::column span="":::
        Paramètre facultatif permettant de choisir une version du modèle d’analyse de texte. Actuellement, seul le modèle par défaut peut être utilisé.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Types d’entités

La reconnaissance d’entité nommée v3 fournit une détection étendue sur plusieurs types. Actuellement, NER v3 peut reconnaître les catégories d’entités suivantes. Pour obtenir la liste détaillée des entités et des langages pris en charge, consultez l’article [Types d’entités nommés](../named-entity-types.md).

* Généralités
* Informations personnelles 

### <a name="request-endpoints"></a>Points de terminaison de requête

La reconnaissance d’entité nommée v3 utilise des points de terminaison distincts pour les demandes de liaison d’entité et NER. Utilisez un format d’URL ci-dessous en fonction de votre demande :

NER
* Entités générales - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entités d’informations personnelles - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Gestion des versions des modèles

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Types pris en charge pour la reconnaissance d’entité nommée v2

> [!NOTE]
> Les entités suivantes sont prises en charge par la reconnaissance d’entité nommée (NER) version 2. [NER v3](#named-entity-recognition-v3-public-preview) est en préversion publique et augmente le nombre et la profondeur des entités reconnues dans le texte.   

| Type  | Subtype | Exemples |
|:-----------   |:------------- |:---------|
| Personne        | N/A\*         | « Jeff », « Bill Gates »     |
| Location      | N/A\*         | « Redmond, Washington », « Paris »  |
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
| DateTime      | Définir           | « Chaque mardi »     |
| URL           | N/A\*         | "https:\//www.bing.com"    |
| Email         | N/A\*         | "support@contoso.com" |
| Numéro de téléphone aux États-Unis  | N/A\*         | (Numéros de téléphone américains uniquement) « (312) 555-0176 » |
| Adresse IP    | N/A\*         | « 10.0.0.100 » |

\* Selon les entités entrées et extraites, certaines entités peuvent omettre le `SubType`.  Tous les types d’entités pris en charge répertoriés sont disponibles uniquement en anglais, chinois simplifié, français, allemand et espagnol.

### <a name="language-support"></a>Support multilingue

L’utilisation de la liaison d'entités dans différentes langues requiert l’utilisation d’une base de connaissances correspondante dans chaque langue. Pour la liaison d'entités dans Analyse de texte, cela signifie que chaque langue prise en charge par le point de terminaison `entities` établira un lien vers le corpus Wikipédia correspondant dans cette langue. Comme la taille du corpus varie d’une langue à l’autre, il est probable que le rappel des fonctionnalités de liaison d'entités varie également. Pour plus d’informations, consultez l’article [Prise en charge linguistique](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition).

## <a name="preparation"></a>Préparation

Vous devez disposer des documents JSON dans ce format : ID, texte, langue

Pour connaître les langues actuellement prises en charge, consultez [cette liste](../text-analytics-supported-languages.md).

La taille des documents doit être inférieure à 5 120 caractères par document et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. L’exemple suivant illustre le contenu qui peut être soumis à l’extrémité de la liaison d'entités.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Étape 1 : Structurer la requête

Vous trouverez plus d’informations sur la définition d’une demande dans [Guide pratique pour appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette requête : [API des entités](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Définissez le point de terminaison HTTP pour l’extraction de phrases clés à l’aide d’une ressource Analyse de texte sur Azure, ou avec un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Vous devez inclure `/text/analytics/v2.1/entities`. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Définissez un en-tête de requête pour inclure la [clé d’accès](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pour les opérations d’Analyse de texte.

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) pour structurer une demande et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et seconde.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.

## <a name="step-3-view-results"></a>Étape 3 : Afficher les résultats

Toutes les demandes POST retournent une réponse au format JSON avec les ID et les propriétés détectées.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données.

Voici un exemple de sortie de liaison d'entités :

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail de liaison d'entités à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

+ L’[API Entités](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/entities`, à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’entités liées (y compris des scores de confiance, des décalages et des liens web, pour chaque ID de document), peut être utilisée dans n’importe quelle application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API Analyse de texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712)
