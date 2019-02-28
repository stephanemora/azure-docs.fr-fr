---
title: 'Tutoriel : Effectuer une reconnaissance des intentions vocales à l’aide du kit de développement logiciel (SDK) Speech pour C#'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous apprenez à reconnaître des intentions vocales avec le kit SDK Speech pour C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 680c10d8402853f1ac2f519b8f07f81b9718ab9e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866995"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Tutoriel : Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

Le [kit SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) de Cognitive Services s’intègre au [Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) pour fournir **la reconnaissance de l’intention**. Une intention est quelque chose que l’utilisateur souhaite faire : réserver un vol, vérifier la météo ou effectuer un appel. L’utilisateur peut utiliser les termes qui lui semblent naturels. À l’aide du machine learning, LUIS mappe les demandes des utilisateurs aux intentions que vous avez définies.

> [!NOTE]
> Une application LUIS définit les intentions et entités à reconnaître. Elle est distincte de l’application C# qui utilise le service Speech. Dans cet article, « appli » fait référence à l’application LUIS tandis que « application » renvoie au code C#.

Dans ce tutoriel, vous utilisez le kit SDK Speech pour développer une application console C# qui dégage des intentions à partir des énoncés d’utilisateur obtenus par le microphone de votre appareil. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Visual Studio faisant référence au package NuGet du kit SDK Speech
> * Créer une configuration de reconnaissance vocale et obtenir un module de reconnaissance de l’intention
> * Obtenir le modèle pour votre appli LUIS et ajouter les intentions dont vous avez besoin
> * Spécifier la langue pour la reconnaissance vocale
> * Utiliser la reconnaissance vocale à partir d’un fichier
> * Utiliser la reconnaissance continue pilotée par événements, asynchrone

## <a name="prerequisites"></a>Prérequis

Assurez-vous de disposer des éléments suivants avant de commencer ce tutoriel.

* Un compte LUIS. Vous pouvez en obtenir un gratuitement via le [portail LUIS](https://www.luis.ai/home).
* Visual Studio 2017 (toute édition)

## <a name="luis-and-speech"></a>LUIS et Speech

LUIS s’intègre au service Speech pour reconnaître les intentions à partir de la reconnaissance vocale. Vous n’avez besoin que de LUIS, aucun abonnement au service Speech n’est nécessaire.

LUIS utilise deux types de clés : 

|Type de clé|Objectif|
|--------|-------|
|création|permet de créer et de modifier des applis LUIS par programmation|
|endpoint |autorise l’accès à une appli LUIS particulière|

La clé du point de terminaison est la clé LUIS nécessaire pour ce tutoriel. Ce tutoriel utilise l’exemple d’appli domotique Home Automation LUIS, que vous pouvez créer en suivant [Utiliser une appli domotique prédéfinie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Si vous avez créé votre propre appli LUIS, vous pouvez aussi l’utiliser.

Lors de la création d’une appli LUIS, une clé de démarrage est générée automatiquement pour que vous puissiez tester l’appli à l’aide de requêtes de texte. Cette clé n’active pas l’intégration du service Speech et ne fonctionne pas avec ce tutoriel. Vous devez créer une ressource LUIS dans le tableau de bord Azure et l’affecter à l’appli LUIS. Vous pouvez utiliser le niveau d’abonnement gratuit pour ce tutoriel. 

Après avoir créé la ressource LUIS dans le tableau de bord Azure, connectez-vous au [portail LUIS](https://www.luis.ai/home), choisissez votre application dans la page My apps (Mes applications) et basculez vers la page Manage (Gérer) de l’appli. Enfin, cliquez sur **Keys and Endpoints** (Clés et points de terminaison) dans la barre latérale.

![Paramètres des clés et point de terminaison du portail LUIS](media/sdk/luis-keys-endpoints-page.png)

Dans la page Keys and Endpoint settings (Paramètres des clés et du point de terminaison) :

1. Faites défiler vers le bas jusqu’à la section des ressources et des clés, puis cliquez sur **Assign resource** (Affecter une ressource).
1. Dans la boîte de dialogue **Assign a key to your app** (Attribuer une clé à votre appli), faites les choix suivants :

    * Choisissez Microsoft en tant que Tenant (Locataire).
    * Sous Subscription Name (Nom de l’abonnement), sélectionnez l’abonnement Azure qui contient la ressource LUIS à utiliser.
    * Sous Key (Clé), choisissez la ressource LUIS à utiliser avec l’appli.

Le nouvel abonnement apparaît dans le tableau, en bas de la page, au bout de quelques instants. Cliquez sur l’icône en regard d’une clé pour la copier dans le Presse-papiers. (Vous pouvez utiliser l’une ou l’autre de ces clés.)

![Clés d’abonnement de l’appli LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Créer un projet de reconnaissance vocale dans Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Ajouter le code

Ouvrez le fichier `Program.cs` dans le projet Visual Studio et remplacez le bloc d’instructions `using` au début du fichier par les déclarations suivantes.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

À l’intérieur de la méthode `Main()` fournie, ajoutez le code suivant.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Créez une méthode asynchrone vide `RecognizeIntentAsync()`, comme indiqué ici.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Dans le corps de cette nouvelle méthode, ajoutez ce code.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Remplacez les espaces réservés de cette méthode par votre clé d’abonnement, votre région et votre ID d’appli LUIS, comme suit.

|Placeholder|Remplacer par|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Votre clé de point de terminaison LUIS. Comme indiqué précédemment, cette clé doit être obtenue à partir de votre tableau de bord Azure ; ce n’est pas une « clé de démarrage ». Vous pouvez la trouver dans la page Keys and Endpoints (Clés et points de terminaison) de votre appli, sous Manage (Gérer) dans le [portail LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|L’identificateur court pour la région de votre abonnement LUIS est, par exemple, `westus` pour la région USA Ouest. Voir [Régions](regions.md).|
|`YourLanguageUnderstandingAppId`|L’ID d’application LUIS. Vous pouvez le trouver sur la page Settings (Paramètres) de votre appli dans le [portail LUIS](https://www.luis.ai/home).|

Ces modifications étant apportées, vous pouvez générer (Ctrl-Maj-B) et exécuter (F5) l’application du tutoriel. Lorsque vous y êtes invité, essayez de dire « Éteins les lumières » dans le micro de votre PC. Le résultat s’affiche dans la fenêtre de console.

Les sections suivantes comportent un développement du code.


## <a name="create-an-intent-recognizer"></a>Créer un module de reconnaissance de l’intention

La première étape de la reconnaissance d’intentions dans la reconnaissance vocale consiste à créer une configuration de reconnaissance vocale à partir de votre région et de votre clé de point de terminaison LUIS. Les configurations de reconnaissance vocale peuvent servir à créer des modules de reconnaissance pour les différentes fonctionnalités du kit SDK Speech. La configuration de reconnaissance vocale offre plusieurs moyens pour spécifier l’abonnement à utiliser. Ici, nous utilisons `FromSubscription`, qui accepte la région et la clé d’abonnement.

> [!NOTE]
> Utilisez la clé et la région de votre abonnement LUIS, et non celles d’un abonnement Speech.

Ensuite, créez un module de reconnaissance de l’intention à l’aide de `new IntentRecognizer(config)`. La configuration sachant déjà quel abonnement utiliser, il est inutile de spécifier à nouveau la clé d’abonnement et le point de terminaison lors de la création du module de reconnaissance.

## <a name="import-a-luis-model-and-add-intents"></a>Importer un modèle LUIS et ajouter des intentions

À présent, importez le modèle à partir de l’appli LUIS avec `LanguageUnderstandingModel.FromAppId()`, et ajoutez les intentions LUIS à reconnaître par le biais de la méthode `AddIntent()` du module de reconnaissance. Ces deux étapes améliorent la précision de la reconnaissance vocale en indiquant les mots que l’utilisateur est susceptible d’utiliser dans ses requêtes. Il n’est pas nécessaire d’ajouter toutes les intentions de l’appli si vous n’avez pas besoin de toutes les identifier dans votre application.

L’ajout d’intentions nécessite trois arguments : le modèle LUIS (qui vient d’être créé et qui est nommé `model`), le nom de l’intention et un ID d’intention. La différence entre l’ID et le nom s’établit comme suit.

|Argument `AddIntent()`|Objectif|
|--------|-------|
|intentName |Nom de l’intention, tel que défini dans l’appli LUIS. Doit correspondre exactement au nom d’intention LUIS.|
|intentID    |ID assigné à une intention reconnue par le kit SDK Speech. Il peut être ce que vous voulez ; il n’est pas nécessaire qu’il corresponde au nom de l’intention, tel que défini dans l’appli LUIS. Par exemple, si plusieurs intentions sont gérées par le même code, vous pouvez utiliser le même ID pour celles-ci.|

L’appli domotique Home Automation LUIS dispose de deux modes : l’un pour mettre un appareil sous tension, l’autre pour le mettre hors tension. Les lignes ci-dessous ajoutent ces intentions au module de reconnaissance de l’intention ; remplacez les trois lignes `AddIntent` dans la méthode `RecognizeIntentAsync()` par ce code.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

## <a name="start-recognition"></a>Démarrer la reconnaissance

Le module de reconnaissance étant créé, et les intentions ajoutées, la reconnaissance peut commencer. Le kit SDK Speech prend en charge la reconnaissance ponctuelle et continue.

|Mode de reconnaissance|Méthodes à appeler|Résultat|
|----------------|-----------------|---------|
|Ponctuel|`RecognizeOnceAsync()`|Retourne l’intention reconnue, si elle existe, après un énoncé.|
|Continue|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Reconnaît plusieurs énoncés. Émet des événements (par exemple, `IntermediateResultReceived`) lorsque les résultats sont disponibles.|

L’application du tutoriel utilise le mode ponctuel et appelle donc `RecognizeOnceAsync()` pour débuter la reconnaissance. Le résultat est un objet `IntentRecognitionResult` contenant des informations sur l’intention reconnue. La réponse JSON de LUIS est extraite par l’expression suivante :

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

L’application du tutoriel n’analyse pas le résultat JSON, elle ne fait que l’afficher dans la fenêtre de console.

![Résultats de la reconnaissance de LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Préciser une langue de reconnaissance

Par défaut, LUIS reconnaît les intentions en anglais (États-Unis) (`en-us`). En attribuant un code de paramètres régionaux à la propriété `SpeechRecognitionLanguage` de la configuration de reconnaissance vocale, vous pouvez reconnaître des intentions dans d’autres langues. Par exemple, ajoutez `config.SpeechRecognitionLanguage = "de-de";` dans notre application de tutoriel avant de créer le module de reconnaissance pour reconnaître les intentions en allemand. Voir [Langues prises en charge](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Reconnaissance continue depuis un fichier

Le code suivant illustre deux caractéristiques supplémentaires de la reconnaissance de l’intention avec le kit SDK Speech. La première, mentionnée précédemment, est la reconnaissance continue, par laquelle le module de reconnaissance émet des événements lorsque des résultats sont disponibles. Ces événements peuvent ensuite être traités par les gestionnaires d’événements que vous fournissez. Avec la reconnaissance continue, vous appelez la méthode `StartContinuousRecognitionAsync()` du module de reconnaissance pour lancer la reconnaissance à la place de `RecognizeOnceAsync()`.

L’autre caractéristique lit l’audio contenant la reconnaissance vocale à traiter à partir d’un fichier WAV. La création d’une configuration audio utilisable lors de la création du module de reconnaissance de l’intention est pour cela nécessaire. Le fichier doit être de canal unique (mono) avec un taux d’échantillonnage de 16 kHz.

Pour essayer ces caractéristiques, remplacez le corps de la méthode `RecognizeIntentAsync()` par le code suivant. 

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Révisez le code pour inclure votre clé de point de terminaison, votre région et votre ID d’appli LUIS, et ajouter des intentions domotiques, comme effectué précédemment. Changez `whatstheweatherlike.wav` pour le nom de votre fichier audio. Procédez ensuite à la génération et à l’exécution.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Retrouvez le code de cet article dans le dossier samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment effectuer une reconnaissance vocale](how-to-recognize-speech-csharp.md)
