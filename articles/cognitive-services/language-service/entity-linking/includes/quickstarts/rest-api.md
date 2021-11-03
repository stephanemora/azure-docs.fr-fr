---
title: 'Démarrage rapide : utilisation de cURL pour appeler l’API REST de liaison d’entités'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide explique comment utiliser l’API REST de liaison d’entités dans Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: e645c1e9e5d96ce4638e9f8216577f8522977609
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029123"
---
[Documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/)


## <a name="prerequisites"></a>Prérequis

* Version actuelle de [cURL](https://curl.haxx.se/).
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource Language"  target="_blank">créez une ressource Language </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

> [!NOTE]
> * Les exemples BASH suivants utilisent le caractère de continuation de ligne `\`. Si votre console ou terminal utilise un caractère de continuation de ligne différent, utilisez ce caractère.
> * Vous trouverez des exemples propres aux différents langages sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
> * Accédez au portail Azure, puis recherchez la clé et le point de terminaison de la ressource Language que vous avez créés à l’étape des prérequis. Ces informations se trouvent dans la page sur **la clé et le point de terminaison** de la ressource, sous **gestion des ressources**. Remplacez ensuite les chaînes dans le code ci-dessous par votre clé et votre point de terminaison.
Pour appeler l’API, vous avez besoin des informations suivantes :


|paramètre  |Description  |
|---------|---------|
|`-X POST <endpoint>`     | Spécifie votre point de terminaison pour accéder à l’API.        |
|`-H Content-Type: application/json`     | Type de contenu pour l’envoi de données JSON.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Spécifie la clé pour accéder à l’API.        |
|`-d <documents>`     | Code JSON contenant les documents que vous souhaitez envoyer.         |

Les commandes cURL suivantes sont exécutées à partir d’un interpréteur de commandes bash. Modifiez ces commandes avec vos propres nom de ressource, clé de ressource et valeurs JSON.


## <a name="entity-linking"></a>Liaison d’entités

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

### <a name="json-response"></a>Réponse JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```
