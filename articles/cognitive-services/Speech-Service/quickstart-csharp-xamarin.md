---
title: 'Démarrage rapide : Reconnaître une entrée vocale, C# (Xamarin) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous créez une application C# Xamarin multiplateforme pour la plateforme Windows universelle (UWP), Android et iOS en utilisant le SDK Speech de Cognitive Services. Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre appareil ou simulateur. L’application est créée avec le package NuGet du SDK Speech et Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675631"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Démarrage rapide : Reconnaître une entrée vocale avec une application Xamarin multiplateforme qui utilise le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-uwp.md), la [synthèse vocale](quickstart-text-to-speech-csharp-uwp.md) et la [traduction vocale](quickstart-translate-speech-uwp.md).

Dans cet article, vous développez une application C# multiplateforme avec Xamarin pour la plateforme Windows universelle (UWP), Android et iOS qui utilise le [SDK Speech](speech-sdk.md) d’Azure Cognitive Services. Le programme transcrit la voix en texte en temps réel à partir du microphone de votre appareil. L’application est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et n’importe quelle édition de Microsoft Visual Studio 2019.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Un PC Windows avec Windows 10 Fall Creators Update (10.0, Build 16299) ou une version ultérieure et avec un microphone opérationnel.
* Une [installation de Xamarin dans Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* Une [installation de Xamarin Android sur Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* Une [installation de Xamarin iOS sur Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Pour cibler Android : 
   * Un appareil Android (ARM32/64, x86 ; API 23 : Android Marshmallow version 6.0 ou ultérieure) [activé pour le développement](https://developer.android.com/studio/debug/dev-options) et avec un microphone opérationnel.
* Pour cibler iOS : 
    * Un appareil iOS (ARM64) ou un simulateur iOS (x64) [activé pour le développement](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) et avec un microphone opérationnel.
* Pour la prise en charge de la build Windows ARM64, installez les [outils de build facultatifs et le SDK Windows 10 pour ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Ajouter un exemple de code pour le projet helloworld commun

Le projet helloworld commun contient des implémentations indépendantes des plateformes pour votre application multiplateforme. Ajoutez maintenant le code XAML qui définit l’interface utilisateur de l’application, puis ajoutez le code C# derrière l’implémentation.

1. Dans l’**Explorateur de solutions**, sous le projet helloworld commun, ouvrez `MainPage.xaml`.

1. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise **Grid** entre `<StackLayout>` et `</StackLayout>` :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. Dans l’**Explorateur de solutions**, ouvrez le fichier source code-behind `MainPage.xaml.cs`. Il se trouve sous `MainPage.xaml`.

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Dans le gestionnaire `OnRecognitionButtonClicked` du fichier source, recherchez la chaîne `YourSubscriptionKey`, puis remplacez-la par la clé de votre abonnement.

1. Dans le gestionnaire `OnRecognitionButtonClicked`, recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.

1. Ensuite, vous devez créer un [service Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), qui est utilisé pour interroger les autorisations du microphone à partir de différents projets de plateforme, comme UWP, Android et iOS. Pour cela, ajoutez un nouveau dossier nommé *Services* sous le projet helloworld, puis créez un fichier source C# sous celui-ci. Vous pouvez cliquer avec le bouton droit sur le dossier *Services*, puis sélectionner **Ajouter** > **Nouvel élément** > **Fichier de code**. Renommez le fichier `IMicrophoneService.cs` et placez tout le code de l’extrait de code suivant dans ce fichier :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Ajouter un exemple de code pour le projet helloworld.Android

Ajoutez maintenant le code C# qui définit la partie de l’application spécifique à Android.

1. Dans l’**Explorateur de solutions**, sous le projet helloworld.Android, ouvrez `MainActivity.cs`.

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Ensuite, ajoutez l’implémentation spécifique à Android pour `MicrophoneService` en créant un dossier *Services* sous le projet helloworld.Android. Après cela, créez un fichier source C# sous celui-ci. Renommez le fichier `MicrophoneService.cs`. Copiez et collez l’extrait de code suivant dans ce fichier :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Ensuite, ouvrez `AndroidManifest.xml` sous le dossier *Properties*. Ajoutez le paramètre uses-permission suivant pour le microphone entre `<manifest>` et `</manifest>` :

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Ajouter un exemple de code pour le projet helloworld.iOS

Ajoutez maintenant le code C# qui définit la partie de l’application spécifique à iOS. Créez également des configurations spécifiques aux appareils Apple dans le projet helloworld.iOS.

1. Dans l’**Explorateur de solutions**, sous le projet helloworld.iOS, ouvrez `AppDelegate.cs`.

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Ensuite, ajoutez l’implémentation spécifique à iOS pour `MicrophoneService` en créant un dossier *Services* sous le projet helloworld.iOS. Après cela, créez un fichier source C# sous celui-ci. Renommez le fichier `MicrophoneService.cs`. Copiez et collez l’extrait de code suivant dans ce fichier :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Ouvrez `Info.plist` sous le projet helloworld.iOS dans l’éditeur de texte. Ajoutez la paire de valeurs de clé suivante sous la section dict :

   <key>NSMicrophoneUsageDescription</key>
   <string>Cet exemple d’application nécessite un accès au microphone</string>

   > [!NOTE] 
   > Si vous générez une application pour un appareil iPhone, vérifiez que `Bundle Identifier` correspond à l’ID d’application du profil de provisionnement de votre appareil. Sinon, la build échoue. Avec iPhoneSimulator, vous pouvez le laisser tel quel.

1. Si vous effectuez une génération sur un PC Windows, établissez une connexion à un appareil Mac pour la génération via **Outils** > **iOS** > **Coupler au Mac**. Suivez l’Assistant d’instructions fourni par Visual Studio pour activer la connexion à l’appareil Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Ajouter un exemple de code pour le projet helloworld.UWP

Ajoutez maintenant le code C# qui définit la partie de l’application spécifique à UWP.

1. Dans l’**Explorateur de solutions**, sous le projet helloworld.UWP, ouvrez `MainPage.xaml.cs`.

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ensuite, ajoutez l’implémentation spécifique à UWP pour `MicrophoneService` en créant un dossier *Services* sous le projet helloworld.UWP. Après cela, créez un fichier source C# sous celui-ci. Renommez le fichier `MicrophoneService.cs`. Copiez et collez l’extrait de code suivant dans ce fichier :

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ensuite, double-cliquez sur le fichier `Package.appxmanifest` sous le projet helloworld.UWP dans Visual Studio. Sous **Capacités**, vérifiez que **Microphone** est sélectionné, puis enregistrez le fichier.

   > [!NOTE] 
   > Si vous voyez l’avertissement « Le fichier de certificat n’existe pas : helloworld.UWP_TemporaryKey.pfx », consultez l’exemple de [reconnaissance vocale](quickstart-csharp-uwp.md) pour plus d’informations.

1. Dans la barre de menus, sélectionnez **Fichier** > **Enregistrer tout** pour enregistrer vos modifications.

## <a name="build-and-run-the-uwp-application"></a>Générer et exécuter l’application UWP

1. Définissez helloworld.UWP comme projet de démarrage. Cliquez avec le bouton droit sur le projet helloworld.UWP, puis sélectionnez **Générer** pour générer l’application. 

1. Sélectionnez **Déboguer** > **Démarrer le débogage** (ou sélectionnez **F5**) pour démarrer l’application. La fenêtre **helloworld** s’affiche.

   ![Exemple d’application de reconnaissance vocale UWP en C# - Démarrage rapide](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Sélectionnez **Activer le microphone**. Quand la demande d’autorisation d’accès apparaît, sélectionnez **Oui**.

   ![Demande d’autorisation d’accès au microphone](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Sélectionnez **Démarrer la reconnaissance vocale** et prononcez une phrase ou quelques mots en anglais dans le micro de votre appareil. Vos paroles sont transmises aux services Speech et transcrites en texte, qui apparaît dans la fenêtre.

   ![Interface utilisateur de la reconnaissance vocale](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Générer et exécuter les applications Android et iOS

La génération et l’exécution des applications Android et iOS dans l’appareil ou le simulateur se font de façon similaire à UWP. Vérifiez que tous les SDK sont installés correctement, comme indiqué dans la section « Prérequis » de cet article.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)
