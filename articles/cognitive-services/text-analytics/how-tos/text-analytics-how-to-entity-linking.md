---
title: Utiliser une reconnaissance d’entité avec l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Découvrez comment reconnaître des entités à l’aide de l’API Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: dfbb31ce9f61ee28fef046120474a6a170906512
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59505573"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte (préversion)

L’[API Reconnaissance d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) accepte un texte non structuré et, pour chaque document JSON, retourne la liste des entités dont l’ambiguïté est levée avec des liens vers des informations supplémentaires sur le web (Wikipedia et Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Liaison d’entités et reconnaissance d’entité nommée

Analytique de texte `entities` prend en charge de point de terminaison toutes deux nommées (NER) la reconnaissance d’entité et de liaison d’entités.

### <a name="entity-linking"></a>Liaison d’entités
La liaison d’entités est la possibilité d’identifier une entité présente dans un texte et de lever l’ambiguïté sur son identité (par exemple, en déterminant si « Mars » fait référence à la planète ou au dieu romain de la guerre). Ce processus nécessite la présence d’une base de connaissances à laquelle les entités reconnues sont liées. Wikipédia est utilisé en tant que base de connaissances pour le point de terminaison `entities` d’Analyse de texte.

Dans Analyse de texte [version 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634), une seule liaison d’entités est disponible.

### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)
La reconnaissance d’entité nommée (NER) est la capacité d’identifier différentes entités dans du texte et de les classer en classes prédéfinies. Les classes d’entités prises en charge sont répertoriées ci-dessous.

Dans la préversion d’Analyse de texte [version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), la liaison d’entités et la reconnaissance d’entité nommée (NER) sont disponibles.

### <a name="language-support"></a>Support multilingue

L’utilisation de la liaison d'entités dans différentes langues requiert l’utilisation d’une base de connaissances correspondante dans chaque langue. Pour la liaison d'entités dans Analyse de texte, cela signifie que chaque langue prise en charge par le point de terminaison `entities` établira un lien vers le corpus Wikipédia correspondant dans cette langue. Comme la taille du corpus varie d’une langue à l’autre, il est probable que le rappel des fonctionnalités de liaison d'entités varie également.

## <a name="supported-types-for-named-entity-recognition"></a>Types pris en charge pour la reconnaissance d’entité nommée

| Type  | Subtype | Exemples |
|:-----------   |:------------- |:---------|
| Personne        | N/A\*         | « Jeff », « Bill Gates »     |
| Lieu      | N/A\*         | « Redmond, Washington », « Paris »  |
| Organisation  | N/A\*         | « Microsoft »   |
| Quantité      | Number        | « 6 », « six »     | 
| Quantité      | Pourcentage    | « 50 % », « cinquante pour cent »| 
| Quantité      | Ordinal       | « 2nd », « second »     | 
| Quantité      | NumberRange   | « 4 à 8 »     | 
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
| DateTime      | TimeZone      |    | 
| URL           | N/A\*         | « https :\//www.bing.com »    |
| Email         | N/A\*         | "support@contoso.com" |

\* Selon les entités entrées et extraites, certaines entités peuvent omettre le `SubType`.



## <a name="preparation"></a>Préparation

Vous devez disposer de documents JSON dans le format : id, texte, langue

Pour connaître les langues actuellement prises en charge, consultez [cette liste](../text-analytics-supported-languages.md).

La taille des documents doit être inférieure à 5 120 caractères par document et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. L’exemple suivant illustre le contenu qui peut être soumis à l’extrémité de la liaison d'entités.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Étape 1 : Structurer la requête

Vous trouverez plus d’informations sur la définition d’une demande dans [Guide pratique pour appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette requête : [API de liaison d’entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Définissez le point de terminaison HTTP pour l’extraction d’entité. Il doit inclure la ressource `/entities` : `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Définissez un en-tête de demande pour inclure la clé d’accès pour les opérations d’Analyse de texte. Pour plus d’informations, consultez [Guide pratique pour rechercher des points de terminaison et des clés d’accès](text-analytics-how-to-access-key.md).

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) pour structurer une demande et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Le service accepte jusqu'à 100 demandes par secondes et 1000 requêtes par minute. Chaque demande peut être au maximum de 1 Mo.

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

+ L’[API Entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un id, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/entities`, à l’aide [d’une clé d’accès et d’un point de terminaison](text-analytics-how-to-access-key.md) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’entités liées (y compris des scores de confiance, des décalages et des liens web, pour chaque ID de document), peut être utilisée dans n’importe quelle application.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
