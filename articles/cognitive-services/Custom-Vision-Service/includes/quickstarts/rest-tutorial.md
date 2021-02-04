---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: fd845778df5ff2f545463e9cb984a739e35f6773
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500218"
---
Bien démarrer avec l’API REST Custom Vision Suivez ces étapes pour appeler l’API et générer un modèle de classification d’images. Vous allez créer un projet, ajouter des étiquettes, entraîner le projet et utiliser l’URL de point de terminaison de prédiction du projet pour le tester programmatiquement. Utilisez cet exemple comme modèle pour générer votre propre application de reconnaissance d’image.

> [!NOTE]
> Custom Vision est plus facile à utiliser par le biais d’un SDK de bibliothèque de client ou en suivant les [conseils basés sur le navigateur](../../get-started-build-detector.md).

Utilisez la bibliothèque de client Custom Vision pour .NET pour :

* Créer un projet de service Custom Vision
* Ajouter des balises au projet
* Charger et étiqueter des images
* Entraîner le projet
* Publier l’itération actuelle
* Tester le point de terminaison de prédiction

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Créer une ressource Custom Vision"  target="_blank">créez une ressource Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour créer une ressource d’entraînement et de prédiction et obtenir vos clés et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison des ressources que vous créez pour connecter votre application Custom Vision. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.


## <a name="create-a-new-custom-vision-project"></a>Créer un projet de service Custom Vision

Vous allez utiliser une commande comme celle qui suit pour créer un projet de classification d’images. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Copiez la commande dans un éditeur de texte et apportez les modifications suivantes :

* Remplacez `{subscription key}` par votre clé d’abonnement Visage valide.
* Remplacez `{endpoint}` par le point de terminaison qui correspond à votre clé d’abonnement.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Remplacez `{name}` par le nom de votre projet.
* Si vous le souhaitez, définissez d’autres paramètres d’URL pour configurer le type de modèle que votre projet utilisera. Pour plus d’options, consultez l’[API Créer un projet](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae).

Vous recevrez une réponse JSON semblable à la suivante. Enregistrez la valeur `"id"` de votre projet dans un emplacement temporaire.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Ajouter des étiquettes au projet

Utilisez la commande suivante pour définir les étiquettes sur lesquelles vous entraînerez le modèle.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Là encore, insérez votre propre clé et votre propre URL de point de terminaison.
* Remplacez `{projectId}` par votre propre ID de projet.
* Remplacez `{name}` par le nom de l’étiquette que vous voulez utiliser.

Répétez ce processus pour toutes les étiquettes que vous souhaitez utiliser dans votre projet. Si vous utilisez les exemples d’images fournis, ajoutez les étiquettes `"Hemlock"` et `"Japanese Cherry"`.

Vous recevrez une réponse JSON semblable à la suivante. Enregistrez la valeur `"id"` de chaque étiquette dans un emplacement temporaire.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Ensuite, téléchargez les exemples d’images pour ce projet. Enregistrez le contenu du [dossier des exemples d’images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) sur votre appareil local.

> [!NOTE]
> Vous avez besoin d’un jeu d’images plus étoffé pour mener à bien votre formation ? Trove, un projet Microsoft Garage, vous permet de collecter et d’acheter des ensembles d’images à des fins d’entraînement. Une fois que vous avez collecté vos images, vous pouvez les télécharger, puis les importer dans votre projet Custom Vision de la manière habituelle. Pour en savoir plus, consultez la [page dédiée à Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3).

Utilisez la commande suivante pour charger les images et appliquer les étiquettes : une fois pour les images « Hemlock » et séparément pour les images « Japanese Cherry ». Pour plus d’options, consultez l’API [Créer des images à partir de données](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5).

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Là encore, insérez votre propre clé et votre propre URL de point de terminaison.
* Remplacez `{projectId}` par votre propre ID de projet.
* Remplacez `{tagArray}` par l’ID d’une l’étiquette.
* Ensuite, renseignez le corps de la requête avec les données binaires des images que vous souhaitez étiqueter.

## <a name="train-the-project"></a>Entraîner le projet

Cette méthode entraîne le modèle sur les images étiquetées que vous avez chargées, et retourne un ID pour l’itération du projet en cours.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Là encore, insérez votre propre clé et votre propre URL de point de terminaison.
* Remplacez `{projectId}` par votre propre ID de projet.
* Remplacez `{tagArray}` par l’ID d’une l’étiquette.
* Ensuite, renseignez le corps de la requête avec les données binaires des images que vous souhaitez étiqueter.
* Utilisez éventuellement d’autres paramètres d’URL. Pour plus d’options, consultez l’API [Entraîner un projet](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1).

> [!TIP]
> Entraîner avec des étiquettes sélectionnées
>
> Vous pouvez, si vous le souhaitez, effectuer l’entraînement sur un sous-ensemble de vos étiquettes appliquées. Vous pouvez procéder de la sorte si vous n’avez pas encore suffisamment appliqué certaines étiquettes, contrairement à d’autres. Ajoutez le contenu JSON facultatif au corps de votre requête. Renseignez le tableau `"selectedTags"` avec les ID des étiquettes que vous souhaitez utiliser.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

La réponse JSON contient des informations sur votre projet entraîné, y compris l’ID d’itération (`"id"`). Enregistrez cette valeur pour l’étape suivante.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Publier l’itération actuelle

Cette méthode rend l’itération actuelle du modèle disponible pour l’interrogation. Vous utilisez le nom du modèle retourné comme référence pour envoyer des demandes de prédiction. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Là encore, insérez votre propre clé et votre propre URL de point de terminaison.
* Remplacez `{projectId}` par votre propre ID de projet.
* Remplacez `{iterationId}` par l’ID renvoyé à l’étape précédente.
* Remplacez `{publishedName}` par le nom que vous souhaitez donner à votre modèle de prédiction.
* Remplacez `{predictionId}` par votre propre ID de ressource de prédiction. Vous le trouverez sous l’onglet **Vue d’ensemble** de votre ressource de prédiction dans le portail Azure, listé comme **ID d’abonnement**.
* Utilisez éventuellement d’autres paramètres d’URL. Consultez l’API [Publier une itération](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5).

## <a name="test-the-prediction-endpoint"></a>Tester le point de terminaison de prédiction

Pour finir, utilisez cette commande afin de tester votre modèle entraîné en chargeant une nouvelle image pour qu’il la classifie avec des étiquettes. Vous pouvez utiliser l’image qui se trouve dans le dossier « Test » des exemples de fichiers que vous avez téléchargés précédemment.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Là encore, insérez votre propre clé et votre propre URL de point de terminaison.
* Remplacez `{projectId}` par votre propre ID de projet.
* Remplacez `{publishedName}` par le nom que vous avez utilisé à l’étape précédente.
* Ajoutez les données binaires de votre image locale au corps de la requête.
* Utilisez éventuellement d’autres paramètres d’URL. Consultez l’API [Classifier une image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3).

La réponse JSON retournée listera chacune des étiquettes appliquées par le modèle à votre image, ainsi que les scores de probabilité pour chaque étiquette. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent effectué toutes les étapes du processus de classification d’images à l’aide de l’API REST. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](../../test-your-model.md)

* Qu’est-ce que Custom Vision ?
* [Documentation de référence d’API (entraînement)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [Documentation de référence d’API (prédiction)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
