---
title: Envoyer une tâche de reconnaissance d’entités nommées (NER) personnalisées
titleSuffix: Azure Cognitive Services
description: Découvrez comment envoyer une demande de reconnaissance d’entités nommées (NER) personnalisées.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 874d7c93a4e884c5bd449c6d49d676147e673978
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096724"
---
# <a name="deploy-a-model-and-extract-entities-from-text-using-the-runtime-api"></a>Déployez un modèle et extrayez des entités à partir du texte en utilisant l’API d’exécution.

Dès que vous êtes satisfait des performances de votre modèle, vous pouvez le déployer et l’utiliser pour reconnaître les entités dans le texte. Vous pouvez uniquement envoyer des tâches de reconnaissance d’entités avec l’API et non à partir de Language Studio.

## <a name="prerequisites"></a>Prérequis

* Un [projet créé](create-project.md) correctement avec un compte de stockage Blob Azure configuré
    * Des données textuelles qui [ont été chargées](create-project.md#prepare-training-data) dans votre compte de stockage.
* Des [données étiquetées](tag-data.md)
* Un [modèle correctement entraîné](train-model.md)
* Avoir passé en revue les [détails de l’évaluation du modèle](view-model-evaluation.md) pour déterminer les performances de votre modèle.
    * (facultatif) [Avoir apporté des améliorations](improve-model.md) à votre modèle si ses performances ne sont pas satisfaisantes.

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).

## <a name="deploy-your-model"></a>Déployer votre modèle

1. Accédez à votre projet dans [Langage Studio](https://aka.ms/custom-extraction)

2. Dans le menu de gauche, sélectionnez **Déployer le modèle**.

3. Sélectionnez le modèle à déployer, puis **Déployer le modèle**.

> [!TIP]
> Vous pouvez tester votre modèle dans Language Studio en envoyant des exemples de texte à classer. 
> 1. Sélectionnez **Tester le modèle** dans le menu de gauche de votre projet dans Language Studio.
> 2. Sélectionnez le modèle à tester.
> 3. Ajoutez votre texte à la zone de texte, vous pouvez également charger un fichier `.txt`. 
> 4. Cliquez sur **Exécuter le test**.
> 5. Sous l’onglet **Résultat**, vous pouvez voir les entités extraites de votre texte. Vous pouvez également voir la réponse JSON sous l’onglet **JSON**.

## <a name="send-a-text-classification-request-to-your-model"></a>Envoyer une demande de classification de texte à votre modèle

# <a name="using-language-studio"></a>[Utilisation de Language Studio](#tab/language-studio)

### <a name="using-language-studio"></a>Utilisation de Language Studio

1. Une fois le déploiement effectué, sélectionnez le modèle à utiliser et, dans le menu supérieur, cliquez sur **Obtenir l’URL de prédiction**, et copiez l’URL et le corps.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-1.png" alt-text="run-inference" lightbox="../../custom-classification/media/get-prediction-url-1.png":::

2. Dans la fenêtre qui s’affiche, sous le pivot **Envoyer**, copiez l’exemple de demande dans la ligne de commande

3. Remplacez `<YOUR_DOCUMENT_HERE>` par le texte à classer.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../../custom-classification/media/get-prediction-url-2.png":::

4. Envoyez la demande.

5. Dans l’en-tête de réponse que vous recevez, extrayez `jobId` à partir de `operation-location`, au format : `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. Copiez la demande de récupération et remplacez `jobId`, puis envoyez la demande.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../../custom-classification/media/get-prediction-url-3.png":::
    
 ## <a name="retrieve-the-results-of-your-job"></a>Récupérer les résultats de votre travail

1. Sélectionnez **Récupérer** dans la même fenêtre où vous avez obtenu l’exemple de demande précédemment et copiez l’exemple de demande dans un éditeur de texte. 

    :::image type="content" source="../media/get-prediction-retrieval-url.png" alt-text="Capture d’écran montrant la demande de récupération de prédiction et l’URL" lightbox="../media/get-prediction-retrieval-url.png":::

2. Remplacez `<OPERATION_ID>` par `jobId` obtenu à l’étape précédente. 

3. Envoyez la demande `GET` cURL dans votre terminal ou invite de commandes. Si la demande a réussi, vous recevez une réponse 202 et un JSON similaire au suivant.

Plus d’informations sur les résultats sont disponibles dans la section suivante.

# <a name="using-the-api"></a>[Utilisation de l’API](#tab/api)

### <a name="using-the-api"></a>Utilisation de l’API

### <a name="get-your-resource-keys-endpoint"></a>Obtenir vos clés de ressource et votre point de terminaison

1. Accédez à la page de vue d’ensemble de votre ressource dans le [portail Azure](https://ms.portal.azure.com/#home)

2. Dans le menu de gauche, sélectionnez **Clés et point de terminaison**. Utilisez le point de terminaison pour les demandes d’API et la clé pour l’en-tête `Ocp-Apim-Subscription-Key`.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Obtenir le point de terminaison Azure" lightbox="../../custom-classification/media/get-endpoint-azure.png":::

### <a name="submit-custom-ner-task"></a>Envoyer une tâche de NER personnalisée

> [!NOTE]
> Le nom du projet respecte la casse.

Utilisez cette demande **POST** pour démarrer une tâche d’extraction d’entité. Remplacez `{projectName}` par le nom du projet où se trouve le modèle à utiliser.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>headers

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé d’abonnement qui fournit l’accès à cette API.|

#### <a name="body"></a>body

```json
    {
    "displayName": "MyJobName",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1", 
                "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
            },
            {
                "id": "doc2",
                "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
            }
        ]
    },
    "tasks": {
        "customEntityRecognitionTasks": [      
            {
                "parameters": {
                      "project-name": "MyProject",
                      "deployment-name": "MyDeploymentName"
                      "stringIndexType": "TextElements_v8"
                }
            }
        ]
    }
}
```

|Clé|Exemple de valeur|Description|
|--|--|--|
|displayName|« MyJobName »|Nom de votre travail|
|dans des documents|[{},{}]|Liste des documents sur lesquels exécuter des tâches|
|id|« doc1 »|identificateur de document sous forme de chaîne|
|text|« Lorem ipsum dolor sit amet »| Votre document sous forme de chaîne|
|« tasks »|[]| Liste des tâches à effectuer.|
|--|customEntityRecognitionTasks|Identificateur de la tâche à effectuer. |
|parameters|[]|Liste de paramètres à passer à la tâche|
|nom-projet| « MyProject »| Nom de votre projet. Le nom du projet respecte la casse.|
|deployment-name| « MyDeploymentName »| Nom de votre déploiement|

#### <a name="response"></a>response

Vous recevez une réponse 202 indiquant la réussite. Dans les **en-têtes** de réponse, extrayez `operation-location`.
`operation-location` est au format suivant :

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

Vous utilisez ce point de terminaison à l’étape suivante pour obtenir les résultats de la tâche de reconnaissance personnalisée.

### <a name="get-task-status-and-results"></a>Obtenir l’état et les résultats de la tâche

Utilisez la demande **GET** suivante pour interroger l’état/les résultats de la tâche de reconnaissance personnalisée. Vous pouvez utiliser le point de terminaison que vous avez reçu à l’étape précédente.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>headers

|Clé|Valeur|
|--|--|
|Ocp-Apim-Subscription-Key| Clé d’abonnement qui fournit l’accès à cette API.|

Plus d’informations sur les résultats sont disponibles dans la section suivante.

---

#### <a name="custom-extraction-task-results"></a>Résultats de la tâche d’extraction personnalisée

La réponse renvoyée par l’appel de résultat de GET est un document JSON avec les paramètres suivants :

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
               "documents": [
                        {
                            "id": "doc1",
                            "entities": [
                                {
                                    "text": "Government",
                                    "category": "restaurant_name",
                                    "offset": 23,
                                    "length": 10,
                                    "confidenceScore": 0.0551877357
                                }
                            ],
                            "warnings": []
                        },
                        {
                            "id": "doc2",
                            "entities": [
                                {
                                    "text": "David Schmidt",
                                    "category": "artist",
                                    "offset": 0,
                                    "length": 13,
                                    "confidenceScore": 0.8022353
                                }
                            ],
                            "warnings": []
                        }
                    ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```
