---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: de0c8fd674872a48a302a76a3f0951df13041206
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82135070"
---
Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec C#, afin de générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des mots clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple comme modèle pour générer votre propre application .NET. Si vous souhaitez passer par le processus de génération et d’utilisation d’un modèle de classification _sans_ code, consultez plutôt l’[aide basée sur le navigateur](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Conditions préalables requises

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Custom Vision et un exemple de code

Pour écrire une application .NET qui utilise le service Vision personnalisée, vous avez besoin des packages NuGet Vision personnalisée. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ImageClassification** et ouvrez le fichier _ImageClassification.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si l’arbre est une __cigüe__ ou un __cerisier du Japon__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>Comprendre le code

Ouvrez le fichier _Program.cs_ et inspectez le code. [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour vos clés de formation et de prédiction nommées `CUSTOM_VISION_TRAINING_KEY` et `CUSTOM_VISION_PREDICTION_KEY`, respectivement. Le script recherchera ces variables.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Récupérez l’URL de votre point de terminaison à partir de la page Paramètres du site web Custom Vision. Enregistrez-la dans une variable d’environnement appelée `CUSTOM_VISION_ENDPOINT`. Le script enregistre une référence à celle-ci à la racine de votre classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Les lignes de code suivantes exécutent la fonctionnalité principale du projet.

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. Consultez la méthode [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Créer un classifieur](../../getting-started-build-a-classifier.md)).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Les images de ce projet sont incluses. Elles sont référencées dans la méthode **LoadImagesFromDisk** dans _Program.cs_. Vous pouvez charger jusqu’à 64 images dans un même lot.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération du modèle de prédiction, puis publie cette itération sur le point de terminaison de prédiction. Vous pouvez utiliser le nom de l’itération pour envoyer des demandes de prédiction. L’itération n’est pas disponible sur le point de terminaison de prédiction tant qu’elle n’est pas publiée.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Définir le point de terminaison de prédiction

Il s’agit de la référence que vous pouvez utiliser pour soumettre une image au modèle actuelle et obtenir une prédiction de classification.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Soumettre une image au point de terminaison de prédiction par défaut

Dans ce script, l’image test est chargée dans la méthode **LoadImagesFromDisk** et la sortie du modèle de prédiction doit être affichée dans la console. La valeur de la variable `publishedModelName` doit correspondre à la valeur « Publié en tant que » figurant sous l’onglet **Performances** du portail Custom Vision. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Exécution de l'application

Lors de l’exécution de l’application, une fenêtre de console s’ouvre et la sortie suivante est écrite :

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Vous pouvez ensuite vérifier que l’image test (trouvée dans **Images/Test/** ) est balisée de façon appropriée. Pour quitter l’application, appuyez sur une touche. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez désormais comment effectuer toutes les étapes du processus de classification d’images dans le code. Cet exemple exécute une itération d’entraînement unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](../../test-your-model.md)
