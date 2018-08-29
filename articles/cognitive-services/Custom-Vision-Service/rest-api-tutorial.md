---
title: Utiliser l’API REST Service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Utiliser l’API REST pour créer, former, tester et exporter un modèle de vision personnalisée.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250245"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Tutoriel : Utiliser l’API REST Vision personnalisée

Découvrez comment utiliser l’API REST Vision personnalisée pour créer, former, tester et exporter un modèle.

Les informations contenues dans ce document montrent comment utiliser un client REST pour travailler avec l’API REST afin d’effectuer l’apprentissage du service Vision personnalisée. Les exemples montrent comment utiliser l’API avec l’utilitaire `curl` à partir d’un environnement bash et `Invoke-WebRequest` à partir de Windows PowerShell.

> [!div class="checklist"]
> * Obtenir les clés
> * Création d’un projet
> * Créer des balises
> * Ajouter des images
> * Effectuer l’apprentissage et tester le modèle
> * Exporter le modèle

## <a name="prerequisites"></a>Prérequis

* Connaissances de base sur REST (Representational State Transfer). Ce document n’aborde pas en détail les éléments tels que les verbes HTTP, JSON ou autres éléments couramment utilisés avec REST.

* Un bash (Bourne Again Shell) avec l’utilitaire [curl](https://curl.haxx.se) ou Windows PowerShell 3.0 (ou version ultérieure).

* Un compte Vision personnalisée. Pour plus d’informations, consultez le document [Créer un classifieur](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Obtenir les clés

Lorsque vous utilisez l’API REST, vous devez vous authentifier à l’aide d’une clé. Lorsque vous effectuez des opérations de formation ou d’administration, vous utilisez la __clé de formation__. Lorsque vous utilisez le modèle pour élaborer des prédictions, vous utilisez la __clé de prédiction__.

Lorsque vous élaborez une requête, la clé est envoyée en tant qu’en-tête de la requête.

Pour obtenir les clés pour votre compte, visitez la [page web Custom Vision](https://customvision.ai) et sélectionnez __l’icône d’engrenage__ dans le coin supérieur droit. Dans la section __Comptes__, copiez les valeurs des champs __Training Key__ (Clé de formation) et __Prediction Key__ (Clé de prédiction).

![Image de l’interface utilisateur des clés](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Dans la mesure où les clés sont utilisées pour authentifier chaque requête, les exemples dans ce document supposent que les valeurs de clé sont contenues dans les variables d’environnement. Utilisez les commandes suivantes pour stocker les clés dans les variables d’environnement avant d’utiliser d’autres extraits de code de ce document :
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Création d'un projet

Les exemples suivants créent un nouveau projet nommé `myproject` dans votre instance de service Vision personnalisée. Par défaut, ce service utilise le domaine `General` :

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> L’entrée `id` dans la réponse est l’ID du nouveau projet. Il est utilisé dans d’autres exemples plus loin dans ce document.

Pour plus d’informations sur cette requête, consultez [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Domaines spécifiques

Pour créer un projet pour un domaine spécifique, vous pouvez fournir __l’ID de domaine__ comme paramètre facultatif. Les exemples suivants montrent comment récupérer une liste des domaines disponibles :

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Pour plus d’informations sur cette requête, consultez [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

L’exemple suivant illustre la création d’un nouveau projet qui utilise le domaine __Landmarks__ :

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Créer des balises

Pour baliser des images, vous devez utiliser un ID de balise. L’exemple suivant montre comment créer une balise nommée `cat` et obtenir un ID de balise. Remplacez `{projectId}` par l’ID de votre projet. Utilisez le paramètre `name=` pour spécifier le nom de la balise :

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La réponse à la requête ressemble au document JSON : 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Enregistrez la valeur `id`, car elle est utilisée lors du balisage des images.

Pour plus d’informations sur cette requête, consultez [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Ajouter des images

Les exemples suivants illustrent l’ajout d’un fichier à partir d’une URL. Remplacez `{projectId}` par l’ID de votre projet. Remplacez `{tagId}` par l’ID de balise de l’image :

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Pour plus d’informations sur cette requête, consultez [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Formation du modèle

Les exemples suivants montrent comment effectuer l’apprentissage du modèle. Remplacez `{projectId}` par l’ID de votre projet :

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Enregistrez la valeur `id`, car elle est utilisée pour tester et exporter le modèle.

Pour plus d’informations, consultez [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Tester le modèle

Les exemples suivants montrent comment effectuer un test du modèle. Remplacez `{projectId}` par l’ID de votre projet. Remplacez `{iterationId}` par l’ID retourné à partir de l’apprentissage du modèle. Remplacez `https://linktotestimage` par le chemin d’accès de l’image de test.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Pour plus d’informations, consultez [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Exporter le modèle

L’exportation du modèle est un processus en deux étapes. Tout d’abord, vous devez spécifier le format du modèle, puis demander l’URL du modèle exporté.

### <a name="request-a-model-export"></a>Demander une exportation de modèle

Les exemples suivants montrent comment exporter un modèle `coreml`. Remplacez `{projectId}` par l’ID de votre projet. Remplacez `{iterationId}` par l’ID retourné à partir de l’apprentissage du modèle.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Pour plus d'informations, consultez [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Télécharger le modèle exporté

Les exemples suivants montrent comment récupérer l’URL du modèle exporté. Remplacez `{projectId}` par l’ID de votre projet. Remplacez `{iterationId}` par l’ID retourné à partir de l’apprentissage du modèle.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

La réponse à la requête ressemble au document JSON suivant :

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Pour plus d’informations, consultez [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).