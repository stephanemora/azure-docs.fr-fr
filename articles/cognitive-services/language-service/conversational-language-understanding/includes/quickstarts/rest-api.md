---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 882355f0c4e6a44300182ffd27eb4cc422422f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096997"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services).
* Version actuelle de [cURL](https://curl.haxx.se/).
* Une ressource de langue. Si vous n’en possédez pas, vous pouvez en créer une sur le [Portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics). Si vous créez une ressource, cliquez sur le lien, suivez la procédure et attendez qu’elle soit déployée. Cliquez ensuite sur **Accéder à la ressource**.

## <a name="get-your-resource-keys-and-endpoint"></a>Récupération des clés et du point de terminaison de la ressource

1. Accédez à la page de présentation de votre ressource sur le [Portail Azure](https://ms.portal.azure.com/#home).
2. Dans le menu de gauche, sélectionnez **Clés et point de terminaison**. Vous utilisez le point de terminaison et la clé pour les demandes d’API 

    :::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Capture d’écran montrant la page de la clé et du point de terminaison sur le Portail Azure." lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="import-a-project"></a>Importer un projet

Pour commencer, vous pouvez importer un fichier JSON CLU dans le service. Le guide de démarrage rapide fournit ci-dessous un exemple de code JSON qui définit quelques intentions et entités pour une application de messagerie électronique appelée « EmailProject ». 

Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour créer votre projet.

### <a name="request-url"></a>URL de la demande

Utilisez l’URL suivante quand vous créez votre demande d’API. Remplacez les valeurs d’espace réservé suivantes par vos valeurs : 

```rest
{YOUR-ENDPOINT}/language/analyze-conversation/projects/EmailProject/:import?api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="body"></a>body

Utilisez l’exemple JSON suivant comme corps.

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "EmailProject",
        "description": "",
        "type": "Conversation",
        "multilingual": true,
        "language": "en-us"
    },
    "assets": {
        "intents": [
            {
                "name": "Read"
            },
            {
                "name": "Delete"
            },
            {
                "name": "Attach"
            }
        ],
        "entities": [
            {
                "name": "Sender"
            },
            {
                "name": "FileName"
            },
            {
                "name": "FileType"
            }
        ],
        "examples": [
            {
                "text": "add the pdf file with the name signed contract",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 31,
                        "length": 15
                    },
                    {
                        "entity": "FileType",
                        "offset": 8,
                        "length": 3
                    }
                ]
            },
            {
                "text": "attach the powerpoint file",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 10
                    }
                ]
            },
            {
                "text": "attach the excel file called reports q1",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 29,
                        "length": 10
                    },
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 5
                    }
                ]
            },
            {
                "text": "move this to the deleted folder",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "remove this one",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete this",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete my last email from martha",
                "language": "en-us",
                "intent": "Delete",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 26,
                        "length": 6
                    }
                ]
            },
            {
                "text": "what did the email from matt say",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 24,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read john's email for me",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 5,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read the email from carol",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 20,
                        "length": 5
                    }
                ]
            }
        ]
    }
}
```

## <a name="start-training-your-model"></a>Lancement de l’apprentissage du modèle

Une fois votre projet importé, vous pouvez commencer l’apprentissage d’un modèle. Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer l’apprentissage.

### <a name="request-url"></a>URL de la demande

Utilisez l’URL suivante quand vous créez votre demande d’API. Remplacez les valeurs d’espace réservé suivantes par vos valeurs : 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/:train?api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|

### <a name="request-body"></a>Corps de la demande

Utilisez l’objet suivant dans votre demande. Le modèle est nommé `MyModel` une fois l’apprentissage effectué.  

```json
{
  "modelLabel":"MyModel",
  "RunVerification":false
}
```

Une fois que vous avez envoyé votre demande d’API, vous recevez une réponse `202` indiquant la réussite. Dans les en-têtes de réponse, extrayez la valeur `location`. Elle est au format suivant : 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` est utilisé pour identifier votre demande, car cette opération est asynchrone. Vous utilisez cette URL à l’étape suivante pour obtenir l’état de l’entraînement. 

## <a name="get-training-status"></a>Obtenir l’état de l’entraînement

Utilisez la demande **GET** suivante pour interroger l’état du processus d’entraînement de votre modèle. Vous pouvez vous servir de l’URL reçue à l’étape précédente ou remplacer les valeurs d’espace réservé ci-dessous par les vôtres. 

### <a name="request-url"></a>URL de la demande

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison permettant d’authentifier votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | ID permettant de localiser l’état d’apprentissage de votre modèle. Il s’agit de la valeur d’en-tête `location` que vous avez reçue à l’étape précédente.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|


### <a name="response-body"></a>Corps de la réponse

Une fois que vous avez envoyé la demande, vous recevez la réponse suivante. Continuez à interroger ce point de terminaison jusqu’à ce que le paramètre **status** passe à « réussi ». 

```json
{
    "result":
    {
          "trainedModelLabel":"MyModel",
          "trainStatus":{"percentComplete":0,"elapsedTime":null},
          "evaluationStatus":{"percentComplete":0,"elapsedTime":null}
     },
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="deploy-your-model"></a>Déployer votre modèle

Une fois l’apprentissage terminé, vous pouvez déployer votre modèle pour effectuer des prédictions. 

Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer à déployer un modèle de compréhension du langage courant.


### <a name="request-url"></a>URL de la demande

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production?api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifier vos demandes d’API.|


### <a name="request-body"></a>Corps de la demande

```json
{
  "trainedModelLabel":"MyModel",
  "deploymentName":"production"
}
```

Une fois que vous avez envoyé votre demande d’API, vous recevez une réponse `202` indiquant la réussite. Dans les en-têtes de réponse, extrayez la valeur `location`. Elle est au format suivant : 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` est utilisé pour identifier votre demande, car cette opération est asynchrone. Cette URL servira à l’étape suivante pour obtenir l’état de l’apprentissage.

## <a name="get-deployment-status"></a>Obtenir l’état d’un déploiement

Utilisez la demande **GET** suivante pour interroger l’état du processus de déploiement de votre modèle. Vous pouvez vous servir de l’URL reçue à l’étape précédente ou remplacer les valeurs d’espace réservé ci-dessous par les vôtres. 

### <a name="request-url"></a>URL de la demande

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison permettant d’authentifier votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | ID permettant de localiser l’état d’apprentissage de votre modèle. Il s’agit de la valeur d’en-tête `location` que vous avez reçue à l’étape précédente.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifiée vos demandes d’API.|


### <a name="response-body"></a>Corps de la réponse

Une fois que vous avez envoyé la demande, vous recevez la réponse suivante. Continuez à interroger ce point de terminaison jusqu’à ce que le paramètre **status** passe à « réussi ». 

```json
{
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="query-model"></a>Interrogation du modèle

Une fois le déploiement terminé, vous pouvez commencer à interroger votre projet pour effectuer des prédictions. 

Créez une demande **POST** en utilisant l’URL, les en-têtes et le corps JSON suivants pour commencer à déployer un modèle de compréhension du langage courant.

### <a name="request-url"></a>URL de la demande

```rest
{YOUR-ENDPOINT}/language/:analyze-conversations?projectName=EmailProject&deploymentName=production&api-version=2021-11-01-preview
```

|Espace réservé  |Valeur  | Exemple |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Point de terminaison pour l’authentification de votre demande d’API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>headers

Utilisez l’en-tête suivant pour authentifier votre demande. 

|Clé|Valeur|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clé de votre ressource. Utilisée pour authentifier vos demandes d’API.|

### <a name="request-body"></a>Corps de la demande

```json
{
  "query":"attach a docx file"
}
```

### <a name="response-body"></a>Corps de la réponse

Une fois que vous avez envoyé la demande, vous recevez la réponse suivante comme prédiction.

```json
{
    "query":"attach a docx file",
    "prediction": {
        "topIntent":"Attach",
        "projectKind":"conversation",
        "intents":[{"category":"Attach","confidenceScore":0.9998592},{"category":"Read","confidenceScore":0.00010551753},{"category":"Delete","confidenceScore":3.5209276E-05}],
        "entities":[{"category":"FileType","text":"docx","offset":9,"length":4,"confidenceScore":1}]
     }
}
```
