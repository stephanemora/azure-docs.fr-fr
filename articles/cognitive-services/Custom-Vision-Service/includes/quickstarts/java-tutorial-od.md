---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 107cc24cc03c7f8716f4ee0577fc2372668adcd9
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604841"
---
Ce guide fournit des instructions et un exemple de code pour vous aider à commencer à utiliser la bibliothèque de client Custom Vision pour Java afin de générer un modèle de détection d’objets. Vous allez créer un projet, ajouter des étiquettes, entraîner le projet et utiliser l’URL de point de terminaison de prédiction du projet pour le tester programmatiquement. Utilisez cet exemple comme modèle pour générer votre propre application de reconnaissance d’image.

> [!NOTE]
> Si vous souhaitez créer et entraîner un modèle de détection d’objets _sans_ écrire de code, consultez à la place les [instructions basées sur le navigateur](../../get-started-build-detector.md).

## <a name="prerequisites"></a>Prérequis

- Un IDE Java de votre choix
- [JDK 7 ou 8](https://aka.ms/azure-jdks) est installé.
- [Maven](https://maven.apache.org/) est installé.
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Obtenir la bibliothèque de client Custom Vision

Pour écrire une application d’analyse d’image avec Custom Vision pour Java, vous avez besoin des packages maven Custom Vision. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

Vous trouverez la bibliothèque de client Custom Vision dans le référentiel central Maven :
- [SDK d’entraînement](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de prédiction](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clonez ou téléchargez le projet [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemples de Kit de développement logiciel (SDK) Java Cognitive Services). Accédez au dossier **Vision/CustomVision/**.

Ce projet Java crée un projet de détection d’objet Custom Vision nommé __Sample Java OD Project__ (Exemple de projet OD Java), accessible via le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si un objet est une **fourchette** ou une **paire de ciseaux**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Le programme est configuré de manière à référencer vos données de clé comme variables d’environnement. Accédez au dossier **Vision/CustomVision** et entrez les commandes PowerShell suivantes pour définir les variables d’environnement. 

> [!NOTE]
> Si vous utilisez un système d’exploitation autre que Windows, consultez [Configuration des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) pour obtenir des instructions.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Examiner le code

Chargez le projet `Vision/CustomVision` dans votre IDE Java, puis ouvrez le fichier _CustomVisionSamples.java_. Recherchez la méthode **runSample** et commentez l’appel de méthode **ImageClassification_Sample**. Cette méthode exécute le scénario de classification d’images, qui n’est pas abordé dans ce guide. La méthode **ObjectDetection_Sample** implémente la fonctionnalité principale de ce démarrage rapide ; accédez à sa définition et examinez le code. 

## <a name="create-a-new-custom-vision-project"></a>Créer un projet de service Custom Vision

Accédez au bloc de code qui crée un client d’entraînement et un projet de détection d’objet. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. Consultez les surcharges de méthode [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Build a detector](../../get-started-build-detector.md) [Créer un détecteur]).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Ajouter des mots clés à votre projet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Lorsque vous appliquez des balises à des images dans des projets de détection d’objet, vous devez préciser la région de chaque objet balisé avec des coordonnées normalisées. Accédez à la définition de la carte `regionMap`. Ce code associe chaque exemple d’image à sa région balisée.

> [!NOTE]
> Si vous ne disposez pas d’un utilitaire permettant d’effectuer des opérations de glisser-déposer pour marquer les coordonnées des régions, vous pouvez utiliser l’IU web sur [Customvision.ai](https://www.customvision.ai/). Dans cet exemple, les coordonnées sont déjà fournies.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ensuite, passez au bloc de code qui ajoute les images au projet. Les images sont lues à partir du dossier **src/main/ressources** du projet et sont chargées dans le service avec leurs balises et coordonnées de la région appropriées.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

L’extrait de code précédent utilise deux fonctions d’assistance qui récupèrent les images comme flux de ressources et les charge dans le service (vous pouvez charger jusqu’à 64 images dans un même lot).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project-and-publish"></a>Entraîner le projet et publier

Ce code crée la première itération du modèle de prédiction, puis publie cette itération sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

## <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Le point de terminaison de prédiction, représenté par l’objet `predictor` ici, est la référence que vous pouvez utiliser pour soumettre une image au modèle actuel et obtenir une prédiction de classification. Dans cet exemple, `predictor` est défini ailleurs à l’aide de la variable d’environnement de la clé de prédiction.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Exécution de l'application

Pour compiler et exécuter la solution à l’aide de Maven, accédez au répertoire du projet (**Vision/CustomVision**) dans une invite de commandes et entrez la commande d’exécution :

```bash
mvn compile exec:java
```

Affichez la sortie de la console pour la journalisation et les résultats de prédiction. Vous pouvez alors vérifier que l’image test est balisée de façon appropriée et que la région de détection est correcte.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent effectué chaque étape du processus de détection d’objet dans le code. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis. Le guide suivant traite de la classification d’images, mais ses principes sont identiques à la détection d’objet.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](../../test-your-model.md)

* Qu’est-ce que Custom Vision ?
* [Documentation de référence du SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)