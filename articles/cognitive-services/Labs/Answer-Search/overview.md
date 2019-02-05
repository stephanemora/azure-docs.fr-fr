---
title: Qu’est-ce que Project Answer Search ?
titlesuffix: Azure Cognitive Services
description: Présentation de Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: cb02c9067e4d672b0aace4caf13e4c8f0d718afb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220193"
---
# <a name="what-is-project-answer-search"></a>Qu’est-ce que Project Answer Search ?
L’API Project Answer Search utilise le point de terminaison Bing v7 pour obtenir des réponses à des requêtes sous forme de questions. Une question comme « Quelle est la circonférence de la terre ? » retourne une réponse avec des informations factuelles.  Une requête concernant une personne, un lieu ou une chose retourne des informations sur l’entité identifiée par la requête. Ces scénarios peuvent être utiles dans des applications telles que des bots de conversation, des applications de messagerie, des lecteurs, etc.  

Les requêtes retournent des réponses qui varient selon le scénario de la requête : les pages web sont toujours retournées, tandis que les [faits](fact-queries.md) et/ou les [entités](entity-queries.md) ne sont retournés que s’ils sont pertinents.

## <a name="endpoint"></a>Point de terminaison
Pour obtenir des réponses à une question ou des informations sur une personne, un lieu ou une chose, envoyez une requête au point de terminaison de l’API Answer Search. Utilisez les en-têtes et les paramètres d’URL pour définir diverses spécifications.  Incluez l’en-tête *Ocp-Apim-abonnement-Key* avec un jeton valide.  Le paramètre market est obligatoire. Seul le marché `en-us` est actuellement pris en charge.

La requête suivante permet d’obtenir des réponses à la question : « Quelle est la circonférence de la terre ? »

GET :
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

Le paramètre d’URL `q=` est requis pour spécifier l’objet de la recherche.

## <a name="response-object"></a>Objet Réponse

La réponse inclut les en-têtes HTTP, les pages web, les faits et/ou les entités.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Conditions d’utilisation
Project Answer Search et Project Video Trends sont soumis aux [Conditions d’utilisation et d’affichage de la Recherche Bing](use-display-requirements.md).

Vous, ou un tiers agissant en votre nom, ne pouvez pas utiliser, conserver, stocker, mettre en cache, partager ou distribuer les données issues de l’API URL Preview à des fins de test, de développement, de formation, de distribution, ni rendre disponible un service ou une fonctionnalité non Microsoft. 

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Attribution de données  

Les réponses Project Answer Search contiennent des informations qui appartiennent à des tiers. Vous êtes chargé de vous assurer que votre utilisation est appropriée, par exemple en respectant toute licence Creative Commons sur laquelle repose votre expérience utilisateur.  
  
Si une réponse ou un résultat inclut les champs `contractualRules`, `attributions` ou `provider`, vous devez attribuer les données. Si la réponse n’inclut aucun de ces champs, aucune attribution n’est requise. Si la réponse inclut le champ `contractualRules` et `attributions` et/ou `provider`, vous devez utiliser les règles contractuelles pour attribuer les données.  
  
L’exemple suivant montre une entité qui inclut une règle contractuelle MediaAttribution et une image qui inclut un champ `provider`. La règle MediaAttribution identifie l’image en tant que cible de la règle, donc vous ignorez le champ `provider` de l’image et utilisez à la place la règle MediaAttribution permettant l’attribution.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Si une règle contractuelle inclut le champ `targetPropertyName`, la règle s’applique uniquement au champ ciblé. Sinon, la règle s’applique à l’objet parent qui contient le champ `contractualRules`.  
  
  
Dans l’exemple suivant, la règle `LinkAttribution` inclut le champ `targetPropertyName`, donc la règle s’applique au champ `description`. Pour les règles qui s’appliquent à des champs spécifiques, vous devez inclure une ligne immédiatement après les données cibles contenant un lien hypertexte vers le site web du fournisseur. Par exemple, pour attribuer la description, incluez une ligne immédiatement après le texte de description contenant un lien hypertexte vers les données sur le site web du fournisseur, dans ce cas créez un lien vers en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Attribution de licence  

Si la liste des règles contractuelles inclut une règle [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), vous devez afficher les avis sur la ligne qui suit immédiatement le contenu auquel la licence s’applique. La règle `LicenseAttribution` utilise le champ `targetPropertyName` pour identifier la propriété à laquelle la licence s’applique.  
  
Vous trouverez ci-dessous un exemple qui inclut une règle `LicenseAttribution`.  
  
![Attribution de licence](./media/licenseattribution.png)  
  
L’avis de licence que vous affichez doit inclure un lien hypertexte vers le site web contenant des informations sur la licence. En règle générale, vous utilisez le nom de la licence en tant que lien hypertexte. Par exemple, si l’avis indique **Texte sous licence CC-BY-SA** et que CC-BY-SA est le nom de la licence, vous en faites un lien hypertexte.  
  
### <a name="link-and-text-attribution"></a>Attribution de texte et de lien  

Les règles [LinkAttribution](reference.md#linkattribution) et [TextAttribution](reference.md#textattribution) sont généralement utilisées pour identifier le fournisseur des données. Le champ `targetPropertyName` identifie le champ auquel la règle s’applique.  
  
Pour attribuer les fournisseurs, incluez une ligne immédiatement après le contenu auquel les attributions s’appliquent (par exemple, le champ ciblé). La ligne doit être clairement étiquetée pour indiquer que les fournisseurs constituent la source des données. Par exemple, « Donnés de : en.wikipedia.org ». Pour les règles `LinkAttribution`, vous devez créer un lien hypertexte vers le site web du fournisseur.  
  
Vous trouverez ci-dessous un exemple qui inclut les règles `LinkAttribution` et `TextAttribution`.  
  
![Attribution de texte de lien](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Attribution média  

Si l’entité contient une image et que vous l’affichez, vous devez fournir un lien cliquable vers le site web du fournisseur. Si l’entité inclut une règle [MediaAttribution](reference.md#mediaattribution), utilisez l’URL de la règle pour créer le lien cliquable. Sinon, utilisez l’URL incluse dans le champ `provider` de l’image pour créer le lien cliquable.  
  
Vous trouverez ci-dessous un exemple qui inclut le champ `provider` d’une image et les règles contractuelles. Étant donné que l’exemple inclut la règle contractuelle, vous ignorez le champ `provider` de l’image et appliquez la règle `MediaAttribution`.  
  
![Attribution média](./media/mediaattribution.png)  

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](c-sharp-quickstart.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)
