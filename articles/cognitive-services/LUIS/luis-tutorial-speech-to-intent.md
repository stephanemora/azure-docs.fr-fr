---
title: Utiliser le Kit de développement logiciel (SDK) Speech C# avec LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Utilisez l’exemple de Kit de développement logiciel (SDK) Speech C# pour parler dans le microphone et recevoir les prédictions de l’intention et des entités de LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: diberry;
ms.openlocfilehash: 286efcd97c0c9ab95a8241215bc36799c486a8b6
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247713"
---
# <a name="integrate-speech-service"></a>Intégrer le service de reconnaissance vocale
Le [service de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) vous permet d’utiliser une seule requête pour recevoir de l’audio et retourner les objets JSON de prédiction de LUIS.

Dans cet article, vous téléchargez et utilisez un projet C# dans Visual Studio pour prononcer un énoncé dans un microphone et recevoir les informations de prédiction de LUIS. Le projet utilise le package [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) Speech, déjà inclus comme référence. 

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin d’importer l’application.

## <a name="create-luis-endpoint-key"></a>Créer une clé de point de terminaison LUIS
Dans le portail Azure, [créer](luis-how-to-azure-subscription.md#create-luis-endpoint-key) une clé **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importer l’application Ressources humaines LUIS
Les intentions et les énoncés de cet article proviennent de l’application Ressources humaines LUIS, disponible dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Téléchargez le fichier [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json), enregistrez-le avec l’extension *.json, puis [importez-le](luis-how-to-start-new-app.md#import-new-app) dans LUIS. 

Cette application dispose d’intentions, d’entités et d’énoncés associés au secteur des ressources humaines. Les exemples d’énoncés sont les suivants :

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Ajouter l’entité préconçue KeyPhrase
Après avoir importé l’application, sélectionnez **Entités**, puis **Gérer des entités préconçues**. Ajoutez l’entité **KeyPhrase**. L’entité KeyPhrase extrait le sujet principal d’un énoncé.

## <a name="train-and-publish-the-app"></a>Former et publier l’application
1. Dans la barre de navigation du coin supérieur droit, sélectionnez le bouton **Former** pour former l’application LUIS.

2. Sélectionnez **Publier** pour aller à la page de publication. 

3. En bas de la page **Publier**, ajoutez la clé LUIS créée dans la section [Créer une clé de point de terminaison LUIS](#create-luis-endpoint-key).

4. Publiez l’application LUIS en sélectionnant le bouton **Publier** à droite de l’emplacement Publier. 

  Sur la page **Publier**, récupérez l’ID de l’application, la région de publication et l’ID d’abonnement de la clé LUIS créée dans la section [Créer une clé de point de terminaison LUIS](#create-luis-endpoint-key). Vous devez modifier le code pour utiliser ces valeurs plus tard dans cet article. 

  Ces valeurs sont toutes incluses dans l’URL de point de terminaison en bas de la page **Publier** de la clé que vous avez créée. 
  
  N’utilisez **pas** la clé gratuite de démarrage pour cet exercice. Seule une clé **Language Understanding** créée dans le portail Azure fonctionne pour cet exercice. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=

## <a name="audio-device"></a>Appareil audio
Cet article utilise l’appareil audio de votre ordinateur. Il peut s’agir d’un casque avec microphone ou d’un appareil audio intégré. Vérifiez les niveaux d’entrée audio pour savoir si vous devez parler plus fort que d’habitude pour que votre appareil audio vous détecte. 

## <a name="download-the-luis-sample-project"></a>Télécharger l’exemple de projet LUIS
 Clonez ou téléchargez le référentiel [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Ouvrez le [projet de conversion de sortie orale en intention](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) avec Visual Studio et restaurez les packages NuGet. Le fichier de solution Visual Studio est .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Le Kit de développement logiciel (SDK) Speech est déjà inclus comme référence. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Capture d’écran de Visual Studio 2017 affichant le package NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modifier le code C#
Ouvrez le fichier **LUIS_samples.cs** et modifiez les variables suivantes :

|Nom de la variable|Objectif|
|--|--|
|luisSubscriptionKey|Correspond à la valeur subscription-key de l’URL du point de terminaison de la page Publier|
|luisRegion|Correspond au premier sous-domaine de l’URL du point de terminaison|
|luisAppId|Correspond à l’itinéraire de l’URL du point de terminaison suivant **apps/**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Capture d’écran de Visual Studio 2017 affichant les variables du fichier LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Le fichier a déjà mappé les intentions de ressources humaines.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Capture d’écran de Visual Studio 2017 affichant les intentions du fichier LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Générez et exécutez l'application. 

## <a name="test-code-with-utterance"></a>Tester le code avec un énoncé
Sélectionnez **1** et dites dans le microphone : « Qui est le manager de John Smith ».

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

L’intention correcte, **GetEmployeeOrgChart**, a été trouvée avec un taux de confiance de 61 %. L’entité KeyPhrase a été retournée. 

Le Kit de développement logiciel (SDK) Speech retourne l’ensemble de la réponse de LUIS. 

## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS Ressources humaines. Sélectionnez les points de suspension (***...***) à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

Rappelez-vous de supprimer le répertoire LUIS-Samples lorsque vous en avez terminé avec l’exemple de code.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Intégrer LUIS à un robot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website