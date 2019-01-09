---
title: 'Démarrage rapide : créer un projet de détection d’objet avec le kit SDK du service Vision personnalisée pour C#'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet et détectez des objets avec le kit de développement logiciel (SDK) .NET avec C#.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 1ffe75b088b3e6db5717746ab2fc00ef4b350e8b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971867"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Démarrage rapide : créer un projet de détection d’objet avec le kit de développement logiciel (SDK) du service Vision personnalisée pour .NET

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec C#, afin de générer un modèle de détection d’objet. Une fois le projet créé, vous pouvez ajouter des régions balisées, charger des images, effectuer l’apprentissage du projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser le point de terminaison pour tester une image par programmation. Utilisez cet exemple comme modèle pour générer votre propre application .NET. 

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Vision personnalisée et des exemples de code
Pour écrire une application .NET qui utilise le service Vision personnalisée, vous avez besoin des packages NuGet Vision personnalisée. Ils sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonez ou téléchargez le projet [Exemples Cognitive Services .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Accédez au dossier **CustomVision/ObjectDetection** et ouvrez _ObjectDetection.csproj_ dans Visual Studio.

Ce projet Visual Studio crée un projet Vision personnalisée nommé __My New Project__ (Nouveau projet), auquel vous pouvez accéder depuis le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un modèle de détection d’objet. Dans ce projet, le modèle est entraîné pour détecter des fourches et des ciseaux dans des images.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Comprendre le code

Ouvrez le fichier _Program.cs_ et inspectez le code. Insérez vos clés d’abonnement dans les définitions appropriées dans la méthode **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Vision personnalisée

L’extrait de code suivant crée un projet de détection d’objet. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Ajouter des balises au projet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Lorsque vous appliquez des balises à des images dans des projets de détection d’objet, vous devez préciser la région de chaque objet balisé avec des coordonnées normalisées. Le code suivant associe chaque exemple d’image à sa région balisée.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Cette carte d’associations est alors utilisée pour charger chaque exemple d’image avec ses coordonnées de région.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

À ce stade, tous les exemples d’images ont été chargés, et chacun dispose d’une balise (**fourche** ou **ciseaux**) et d’un rectangle associé à la balise.

### <a name="train-the-project"></a>Entraîner le projet

Ce code crée la première itération dans le projet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>Définir l’itération actuelle comme itération par défaut

Ce code marque l’itération actuelle comme itération par défaut. L’itération par défaut correspond à la version du modèle qui répondra aux requêtes de prédiction. Vous devez la mettre à jour à chaque fois que vous réentraînez le modèle.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>Créer un point de terminaison de prédiction

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Cette partie du script charge l’image test, interroge le point de terminaison du modèle, et renvoie des données de prédiction à la console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>Exécution de l'application

Lors de l’exécution de l’application, une fenêtre de console s’ouvre et la sortie suivante est écrite :

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```
Vous pouvez alors vérifier que l’image test (trouvée dans **Images/Test/**) est balisée de façon appropriée et que la région de détection est correcte. À ce stade, vous pouvez appuyer sur n’importe quelle touche pour quitter l’application.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la détection d’objet dans le code. Cet exemple exécute une itération d’entraînement unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision. Le guide suivant traite de la classification d’images, mais ses principes sont identiques à la détection d’objet.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
