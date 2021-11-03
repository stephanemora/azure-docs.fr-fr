---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: e2ba30b020ae089ade114d1470f7e4e85d1327ef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028890"
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


## <a name="text-summarization"></a>Synthèse de texte

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/analyze \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-key-here" \
-d \
' 
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
'
```

Obtenez l’emplacement de l’opération (`operation-location`) de l’en-tête de la réponse. La valeur ressemblera à l’URL suivante :

```http
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/12345678-1234-1234-1234-12345678
```

Pour obtenir les résultats de la requête, utilisez la commande cURL suivante. Veillez à remplacer `my-job-id` par la valeur d’ID numérique que vous avez reçue de l’en-tête de réponse `operation-location` précédent :

```bash
curl -X GET    https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/analyze/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-key-here"
```


### <a name="json-response"></a>Réponse JSON

```json
{
   "jobId":"da3a2f68-eb90-4410-b28b-76960d010ec6",
   "lastUpdateDateTime":"2021-08-24T19:15:47Z",
   "createdDateTime":"2021-08-24T19:15:28Z",
   "expirationDateTime":"2021-08-25T19:15:28Z",
   "status":"succeeded",
   "errors":[
      
   ],
   "displayName":"NA",
   "tasks":{
      "completed":1,
      "failed":0,
      "inProgress":0,
      "total":1,
      "extractiveSummarizationTasks":[
         {
            "lastUpdateDateTime":"2021-08-24T19:15:48.0011189Z",
            "taskName":"ExtractiveSummarization_latest",
            "state":"succeeded",
            "results":{
               "documents":[
                  {
                     "id":"1",
                     "sentences":[
                        {
                           "text":"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                           "rankScore":1.0,
                           "offset":0,
                           "length":160
                        },
                        {
                           "text":"In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                           "rankScore":0.9582327572675664,
                           "offset":324,
                           "length":192
                        },
                        {
                           "text":"At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                           "rankScore":0.9294747193132348,
                           "offset":517,
                           "length":203
                        }
                     ],
                     "warnings":[
                        
                     ]
                  }
               ],
               "errors":[
                  
               ],
               "modelVersion":"2021-08-01"
            }
         }
      ]
   }
}
```
