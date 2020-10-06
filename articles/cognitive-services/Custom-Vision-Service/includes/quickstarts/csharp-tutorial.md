---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 28bcaa898bbf6621295bc5096a924d39073e727e
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604965"
---
Ce guide fournit des instructions et un exemple de code pour vous aider à commencer à utiliser la bibliothèque de client Custom Vision pour C# afin de générer un modèle de classification d’images. Vous allez créer un projet, ajouter des étiquettes, entraîner le projet et utiliser l’URL de point de terminaison de prédiction du projet pour le tester programmatiquement. Utilisez cet exemple comme modèle pour générer votre propre application de reconnaissance d’image.

> [!NOTE]
> Si vous souhaitez créer et entraîner un modèle de classification _sans_ écrire de code, consultez à la place les [instructions basées sur le navigateur](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Installer la bibliothèque de client Custom Vision

Pour écrire une application d’analyse d’image avec Custom Vision pour .NET, vous avez besoin des packages NuGet Custom Vision. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ImageClassification** et ouvrez le fichier _ImageClassification.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si l’arbre est une __cigüe__ ou un __cerisier du Japon__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Examiner le code

Ouvrez le fichier _Program.cs_ et inspectez le code. [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour vos clés de formation et de prédiction nommées `CUSTOM_VISION_TRAINING_KEY` et `CUSTOM_VISION_PREDICTION_KEY`, respectivement. Le script recherchera ces variables.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Récupérez l’URL de votre point de terminaison à partir de la page Paramètres du site web Custom Vision. Enregistrez-la dans une variable d’environnement appelée `CUSTOM_VISION_ENDPOINT`. Le script enregistre une référence à celle-ci à la racine de votre classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Les lignes de code suivantes exécutent la fonctionnalité principale du projet.

## <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. Consultez la méthode [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Créer un classifieur](../../getting-started-build-a-classifier.md)).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Les images de ce projet sont incluses. Elles sont référencées dans la méthode **LoadImagesFromDisk** dans _Program.cs_. Vous pouvez charger jusqu’à 64 images dans un même lot.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

## <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération du modèle de prédiction, puis publie cette itération sur le point de terminaison de prédiction. Vous pouvez utiliser le nom de l’itération pour envoyer des demandes de prédiction. L’itération n’est pas disponible sur le point de terminaison de prédiction tant qu’elle n’est pas publiée.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

## <a name="set-the-prediction-endpoint"></a>Définir le point de terminaison de prédiction

Il s’agit de la référence que vous pouvez utiliser pour soumettre une image au modèle actuelle et obtenir une prédiction de classification.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>Tester le point de terminaison de prédiction

Dans ce script, l’image test est chargée dans la méthode **LoadImagesFromDisk** et la sortie du modèle de prédiction doit être affichée dans la console. La valeur de la variable `publishedModelName` doit correspondre à la valeur « Publié en tant que » figurant sous l’onglet **Performances** du site web Custom Vision. 

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

Vous pouvez ensuite vérifier que l’image test (trouvée dans **Images/Test/**) est balisée de façon appropriée. Pour quitter l’application, appuyez sur une touche. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent effectué toutes les étapes du processus de classification d’images dans le code. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](../../test-your-model.md)

* Qu’est-ce que Custom Vision ?
* [Documentation de référence du SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)