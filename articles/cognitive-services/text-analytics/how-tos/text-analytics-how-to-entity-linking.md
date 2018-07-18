---
title: Guide pratique pour utiliser la liaison d'entités dans l’API REST Analyse de texte (Microsoft Cognitive Services sur Azure) | Microsoft Docs
description: Découvrez comment identifier et résoudre des entités à l’aide de l’API REST Analyse de texte dans Microsoft Cognitive Services sur Azure dans ce didacticiel pas à pas.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368729"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Guide pratique pour identifier les entités liées dans Analyse de texte (préversion)

L’[API Liaison d'entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) accepte un texte non structuré et, pour chaque document JSON, retourne la liste des entités dont l’ambiguïté est levée avec des liens vers des informations supplémentaires sur le web (Wikipedia et Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Liaison d’entités / Reconnaissance d’entité nommée

Dans un traitement en langage naturel, les concepts de liaison d’entités et de reconnaissance d’entité nommée (NER) peuvent facilement prêter à confusion. Dans la préversion du point de terminaison `entities` d’Analyse de texte, seule la liaison d'entités est prise en charge.

La liaison d'entités est la possibilité d’identifier une entité présente dans un texte et de lever l’ambiguïté sur son identité (par exemple, en déterminant si « Mars » fait référence à la planète ou au dieu romain de la guerre). Ce processus nécessite la présence d’une base de connaissances à laquelle les entités reconnues sont liées. Wikipédia est utilisé en tant que base de connaissances pour le point de terminaison `entities` d’Analyse de texte.

### <a name="language-support"></a>Support multilingue

L’utilisation de la liaison d'entités dans différentes langues requiert l’utilisation d’une base de connaissances correspondante dans chaque langue. Pour la liaison d'entités dans Analyse de texte, cela signifie que chaque langue prise en charge par le point de terminaison `entities` établira un lien vers le corpus Wikipédia correspondant dans cette langue. Comme la taille du corpus varie d’une langue à l’autre, il est probable que le rappel des fonctionnalités de liaison d'entités varie également.


## <a name="preparation"></a>Préparation

Vous devez disposer de documents JSON dans le format : id, texte, langue

Pour les langues actuellement prises en charge, consultez [cette liste](../text-analytics-supported-languages.md).

La taille des documents doit être inférieure à 5 000 caractères par document et vous pouvez avoir jusqu'à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. L’exemple suivant illustre le contenu qui peut être soumis à l’extrémité de la liaison d'entités.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Étape 1 : Structuration de la demande

Vous trouverez plus d’informations sur la définition d’une demande dans [Guide pratique pour appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette demande : [API Liaison d'entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Définissez le point de terminaison HTTP pour l’extraction d’expressions clés. Il doit inclure la ressource `/entities` : `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Définissez un en-tête de demande pour inclure la clé d’accès pour les opérations d’Analyse de texte. Pour plus d’informations, consultez [Guide pratique pour rechercher des points de terminaison et des clés d’accès](text-analytics-how-to-access-key.md).

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) pour structurer une demande et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publication de la demande

L’analyse est effectuée à la réception de la demande. Le service accepte jusqu'à 100 demandes par minute. Chaque demande peut être au maximum de 1 Mo.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.

## <a name="step-3-view-results"></a>Étape 3 : Affichage des résultats

Toutes les demandes POST retournent une réponse au format JSON avec les ID et les propriétés détectées.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données.

Voici un exemple de sortie de liaison d'entités :

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Lorsqu’ils sont disponibles, la réponse inclut l’ID Wikipédia, l’URL Wikipédia et l’ID Bing de chaque entité détectée. Ils peuvent être utilisés pour améliorer encore votre application avec des informations relatives à l’entité liée.


## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail de liaison d'entités à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

+ L’[API Liaison d'entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un id, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/entities`, à l’aide [d’une clé d’accès et d’un point de terminaison](text-analytics-how-to-access-key.md) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’entités liées (y compris des scores de confiance, des décalages et des liens web, pour chaque ID de document), peut être utilisée dans n’importe quelle application.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Forum Aux Questions (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page de produit Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API Analyse de texte](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
