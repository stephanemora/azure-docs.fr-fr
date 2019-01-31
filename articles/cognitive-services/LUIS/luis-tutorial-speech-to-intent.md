---
title: Utiliser le SDK de reconnaissance vocale C#
titleSuffix: Azure Cognitive Services
description: Le service de reconnaissance vocale vous permet d’utiliser une seule requête pour recevoir de l’audio et retourner les objets JSON de prédiction de LUIS. Dans cet article, vous téléchargez et utilisez un projet C# dans Visual Studio pour prononcer un énoncé dans un microphone et recevoir les informations de prédiction de LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: df81c892df29b1ffd5fab2e93c82f664c7b613b7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216159"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Intégrer le service Speech à votre Language Understanding
Le [service de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) vous permet d’utiliser une seule requête pour recevoir de l’audio et retourner les objets JSON de prédiction de LUIS. Dans cet article, vous téléchargez et utilisez un projet C# dans Visual Studio pour prononcer un énoncé dans un microphone et recevoir les informations de prédiction de LUIS. Le projet utilise le package [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) Speech, déjà inclus comme référence. 

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin d’importer l’application.

## <a name="create-luis-endpoint-key"></a>Créer une clé de point de terminaison LUIS
Dans le portail Azure, [créer](luis-how-to-azure-subscription.md) une clé **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importer l’application Ressources humaines LUIS
Les intentions et les énoncés de cet article proviennent de l’application Ressources humaines LUIS, disponible dans le dépôt GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Téléchargez le fichier [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), enregistrez-le avec l’extension `.json`, puis [importez-le](luis-how-to-start-new-app.md#import-new-app) dans LUIS. 

Cette application dispose d’intentions, d’entités et d’énoncés associés au secteur des ressources humaines. Les exemples d’énoncés sont les suivants :

|Exemples d’énoncés|
|--|
|Qui est le responsable de John Smith ?|
|De qui John Smith est-il responsable ?|
|Où se trouve le formulaire 123456 ?|
|Ai-je des congés payés ?|


## <a name="add-keyphrase-prebuilt-entity"></a>Ajouter l’entité préconçue KeyPhrase
Après avoir importé l’application, sélectionnez **Entités**, puis **Ajouter une entité prédéfinie**. Ajoutez l’entité **KeyPhrase**. L’entité KeyPhrase extrait le sujet principal d’un énoncé.

## <a name="train-and-publish-the-app"></a>Former et publier l’application
1. Dans la barre de navigation du coin supérieur droit, sélectionnez le bouton **Former** pour former l’application LUIS.

2. Sélectionnez **Manage (Gérer)** dans la barre en haut à droite, puis **Keys and endpoints (Clés et points de terminaison)** dans la navigation gauche. 

3. Dans la page **Keys and endpoints**, affectez la clé LUIS créée dans la section [Create LUIS endpoint key (Créer une clé de point de terminaison LUIS)](#create-luis-endpoint-key).

  Dans cette page, récupérez l’ID de l’application, la région de publication et l’ID d’abonnement de la clé LUIS créée dans la section [Create LUIS endpoint key](#create-luis-endpoint-key). Vous devez modifier le code pour utiliser ces valeurs plus tard dans cet article. 
  
  N’utilisez **pas** la clé gratuite de démarrage pour cet exercice. Seule une clé **Language Understanding** créée dans le portail Azure fonctionne pour cet exercice. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Publiez l’application LUIS en sélectionnant le bouton **Publish** dans la barre en haut à droite. 

## <a name="audio-device"></a>Appareil audio
Cet article utilise l’appareil audio de votre ordinateur. Il peut s’agir d’un casque avec microphone ou d’un appareil audio intégré. Vérifiez les niveaux d’entrée audio pour savoir si vous devez parler plus fort que d’habitude pour que votre appareil audio vous détecte. 

## <a name="download-the-luis-sample-project"></a>Télécharger l’exemple de projet LUIS
 Clonez ou téléchargez le dépôt [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Ouvrez le [projet de conversion de sortie orale en intention](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) avec Visual Studio et restaurez les packages NuGet. Le fichier de solution Visual Studio est documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Le Kit de développement logiciel (SDK) Speech est déjà inclus comme référence. 

[![Capture d’écran de Visual Studio 2017 affichant le package NuGet Microsoft.CognitiveServices.Speech](./media/luis-tutorial-speech-to-intent/nuget-package.png "Capture d’écran de Visual Studio 2017 affichant le package NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modifier le code C#
Ouvrez le fichier `Program.cs` et modifiez les variables suivantes :

|Nom de la variable|Objectif|
|--|--|
|LUIS_assigned_endpoint_key|Correspond à la valeur subscription-key assignée de l’URL du point de terminaison de la page Publier|
|LUIS_endpoint_key_region|Correspond au premier sous-domaine de l’URL du point de terminaison, par exemple `westus`|
|LUIS_app_ID|Correspond à l’itinéraire de l’URL du point de terminaison suivant **apps/**|

Le fichier `Program.cs` a déjà mappé les intentions de ressources humaines.

Générez et exécutez l'application. 

## <a name="test-code-with-utterance"></a>Tester le code avec un énoncé
Dites dans le microphone « Quels sont les dentistes approuvés à Redmond ? ».

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

L’intention correcte, **GetEmployeeBenefits**, a été trouvée avec un taux de confiance de 85 %. L’entité KeyPhrase a été retournée. 

Le Kit de développement logiciel (SDK) Speech retourne l’ensemble de la réponse de LUIS. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS Ressources humaines. Pour ce faire, sélectionnez l’application, puis dans la barre d’outils contextuelle au-dessus de la liste, choisissez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

Rappelez-vous de supprimer le répertoire quand vous en avez terminé avec l’exemple de code.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Intégrer LUIS à un robot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
