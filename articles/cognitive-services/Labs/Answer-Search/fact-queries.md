---
title: 'Démarrage rapide : Requête relative à des faits Project Answer Search'
titlesuffix: Azure Cognitive Services
description: Requêtes relatives à des faits à l’aide de la Recherche des réponses de projet
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: ae8a42b95df70cff9cb845d4d553fbbb33cbdec9
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868332"
---
# <a name="quickstart-query-for-facts"></a>Démarrage rapide : Requête relative à des faits

Si la requête concerne un fait comme une date ou une connaissances identifiable, la réponse peut contenir `facts` réponses. Les réponses relatives aux faits contiennent des résultats pertinents extraits des paragraphes dans les documents web.  Ces requêtes retournent toujours des pages web, et les [faits](fact-queries.md) et/ou [entités](entity-queries.md) dépendent de la requête.

Les requêtes comme Saint-Valentin+2016 ou quand+est+ramadan sont considérées comme des requêtes liées à une date. Si Bing détermine que la requête est liées à une date, la réponse contient une réponse `facts`. 

L’exemple suivant est une `facts` réponse liée à une date. 

**Requête :**
````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

````

**Réponse :** le champ `subjectName` contient une version de l’affichage de la requête de l’utilisateur que vous pouvez utiliser comme étiquette lors de l’affichage du fait. Si la chaîne de requêtes est Saint-Valentin+2016, Bing peut la transformer en Jour de la Saint-Valentin 2016. Le champ de description contient le fait.

````
{   
    "_type" : "SearchResponse",   
    "queryContext" : {   
        "originalQuery" : "valentines 2016" 
    },   
    "facts" : {   
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",   
        "value" : [{   
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",   
            "subjectName" : "Valentine's Day 2016"   
        }]   
    },   
    "rankingResponse" : {   
        "mainline" : {   
            "items" : [{   
                "answerType" : "Facts",   
                "value" : {   
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"                   }   
            }]   
        }   
    }   
}   

````

La requête « Pourquoi le ciel est bleu ? » retourne un exemple de réponse liée à des connaissances.

**Requête :**

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

````

**Réponse :** le champ `value/description` contient la connaissance ou l’information demandée par la requête.

````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

````

## <a name="tabular-data"></a>Données tabulaires
Dans certains cas, les faits peuvent être retournés en tant que `_type: StructuredValue/TabularData`. La requête suivante obtient des données tabulaires avec des informations contrastées sur le café et le thé.

````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us 

````
Les résultats `facts` incluent les cellules et les lignes suivantes :
````
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "http://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

````

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](c-sharp-quickstart.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)
