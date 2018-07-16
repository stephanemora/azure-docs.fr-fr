---
title: Prise en main de l’API Reconnaissance vocale de Microsoft à l’aide de la bibliothèque de bureau C# | Microsoft Docs
description: Développez des applications Windows qui utilisent l’API Reconnaissance vocale de Microsoft pour convertir du contenu audio en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368417"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Prise en main de l’API Reconnaissance vocale en C# pour .NET sur Windows

Cette page explique comment développer une application Windows basique qui utilise l’API Reconnaissance vocale pour convertir du contenu audio en texte. La bibliothèque de client permet la diffusion en continu en temps réel, ce qui signifie que quand votre application cliente envoie du contenu audio au service, elle reçoit simultanément et de manière asynchrone des résultats de reconnaissance partiels.

Les développeurs qui souhaitent utiliser le service Speech à partir d’applications qui s’exécutent sur un appareil peuvent utiliser la bibliothèque de bureau C#. Pour utiliser la bibliothèque, installez le [package NuGetMicrosoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) pour une plateforme 32 bits et le [package NuGet Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) pour une plateforme 64 bits. Pour obtenir des informations de référence sur l’API de la bibliothèque de client, consultez la [bibliothèque de bureau C# Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Les sections suivantes décrivent comment installer, générer et exécuter l’exemple d’application C# à l’aide de la bibliothèque de client C#.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Exigences relatives à la plateforme

L’exemple suivant a été développé pour Windows 8+ et .NET Framework 4.5+ à l’aide de [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obtenir l’exemple d’application

Clonez l’exemple à partir du référentiel de [l’exemple de bibliothèque de bureau C# Speech](https://github.com/microsoft/cognitive-speech-stt-windows).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>S’abonner à l’API Reconnaissance vocale et obtenir une clé d’abonnement d’essai

L’API Microsoft Speech fait partie de Cognitive Services (anciennement Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

> [!IMPORTANT]
> * Obtenez une clé d’abonnement. Pour pouvoir utiliser les bibliothèques de client Speech, vous devez disposer d’une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilisez votre clé d’abonnement. Avec l’exemple d'application de bureau C#, collez votre clé d’abonnement dans la zone de texte lors de l’exécution de l’exemple. Pour plus d’informations, consultez [Exécuter l’exemple d’application](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Étape 1 : Installer l’exemple d’application

1. Démarrez Visual Studio 2015 et sélectionnez **Fichier** > **Ouvrir** > **Projet/Solution**.

2. Accédez au dossier où vous avez enregistré les fichiers de l’API Reconnaissance vocale téléchargés. Sélectionnez **Voix** > **Windows**, puis sélectionnez le dossier Sample-WP.

3. Double-cliquez pour ouvrir le fichier Visual Studio 2015 Solution (.sln) nommé SpeechToText-WPF-Samples.sln. La solution s’ouvre dans Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Étape 2 : Générer l’exemple d’application

1. Si vous souhaitez utiliser la *reconnaissance de l’intention*, vous devez d’abord vous inscrire au [service LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Utilisez ensuite l’URL de point de terminaison de votre application LUIS pour définir la valeur de la clé `LuisEndpointUrl` dans le fichier app.config de l’application dans le dossier samples/SpeechRecognitionServiceExample. Pour plus d’informations sur l’URL de point de terminaison de l’application LUIS, consultez [Publish your app](../../luis/luis-get-started-create-app.md#publish-your-app) (Publier votre application).

   > [!TIP]
   > Remplacez le caractère `&` dans l’URL de point de terminaison LUIS par `&amp;` pour garantir que l’URL est correctement interprétée par l’analyseur XML.

2. Appuyez sur Ctrl+Maj+B ou sélectionnez **Générer** dans le menu du ruban. Ensuite, sélectionnez **Générer la solution**.

## <a name="step-3-run-the-sample-application"></a>Étape 3 : Exécuter l’exemple d’application

1. Une fois la génération terminée, appuyez sur F5 ou sélectionnez **Démarrer** dans le menu du ruban pour exécuter l’exemple.

2. Accédez à la fenêtre**Project Oxford Speech to Text Sample** (Exemple Reconnaissance vocale Project Oxford). Collez votre clé d’abonnement dans la zone de texte **Paste your subscription key here to start** (Collez votre clé d’abonnement ici pour commencer), comme indiqué. Pour conserver votre clé d’abonnement sur votre PC ou ordinateur portable, sélectionnez **Save Key** (Enregistrer la clé). Pour supprimer la clé d’abonnement du système, sélectionnez **Supprimer la clé** pour la supprimer de votre PC ou ordinateur portable.

   ![Coller la clé de reconnaissance vocale](../Images/SpeechRecog_paste_key.PNG)

3. Sous **Speech Recognition Source** (Source de reconnaissance vocale), choisissez une des six sources vocales, réparties en deux catégories d’entrée principales :

   * Utilisez le microphone de votre ordinateur ou un microphone attaché pour capturer le contenu vocal.
   * Lisez un fichier audio.

   Chaque catégorie comporte trois modes de reconnaissance :

    * Mode **ShortPhrase** : énoncé de 15 secondes maximum. À mesure que les données sont envoyées au serveur, le client reçoit de multiples résultats partiels et un résultat final avec plusieurs n meilleurs choix.
    * **Mode LongDictation** : énoncé de deux minutes maximum. À mesure que les données sont envoyées au serveur, le client reçoit plusieurs résultats partiels et plusieurs résultats finaux, selon les emplacements où le serveur indique des pauses dans la phrase.
    * **Intent detection** (Détection d’intention): le serveur renvoie davantage d’informations structurées sur les entrées vocales. Pour utiliser la détection d’intention, vous devez d’abord former un modèle à l’aide du service [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Utilisez les exemples de fichiers audio avec cet exemple d’application. Recherchez les fichiers dans le référentiel que vous avez téléchargé avec cet exemple sous le dossier samples/SpeechRecognitionServiceExample. Ces exemples de fichiers audio s’exécutent automatiquement si aucun autre fichier n’est choisi lorsque vous sélectionnez **Use wav file for Shortphrase mode** (Utiliser un fichier wav pour le mode Shortphrase) ou **Use wav file for Longdictation mode** (Utiliser un fichier wav pour le mode Longdictation) en tant qu’entrée vocale. Actuellement, seul le format audio WAV est pris en charge.

![Interface de la reconnaissance vocale](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exemples expliqués

### <a name="recognition-events"></a>Événements de reconnaissance

* **Événements de résultats partiels** : cet événement est appelé chaque fois que le service Speech prédit ce que vous pouvez dire, avant même que vous n’ayez terminé de parler (si vous utilisez `MicrophoneRecognitionClient`) ou d’envoyer les données (si vous utilisez `DataRecognitionClient`).
* **Événements d’erreur** : cet événement est appelé quand le service détecte une erreur.
* **Événements d’intention** : cet événement est appelé sur les clients « WithIntent » (uniquement en mode ShortPhrase) une fois le résultat final de la reconnaissance converti en intention JSON structurée.
* **Événements de résultat** :
  * En mode `ShortPhrase`, cet événement est appelé et renvoie les n meilleurs résultats une fois que vous avez terminé de parler.
  * En mode `LongDictation`, le gestionnaire d’événements est appelé plusieurs fois, selon les emplacements où le service identifie des pauses dans la phrase.
  * **Pour chacun des n meilleurs choix**, une valeur de confiance et plusieurs formes différentes du texte reconnu sont renvoyées. Pour plus d’informations, consultez [Format de sortie](../Concepts.md#output-format).

Les gestionnaires d'événements sont déjà identifiés dans le code sous la forme de commentaires de code.

## <a name="related-topics"></a>Rubriques connexes

* [Informations de référence sur la bibliothèque de bureau Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Bien démarrer avec l’API Reconnaissance vocale Microsoft en Java sur Android](GetStartedJavaAndroid.md)
* [Bien démarrer avec l’API Reconnaissance vocale en Objective-C sur iOS](Get-Started-ObjectiveC-iOS.md)
* [Bien démarrer avec l’API Reconnaissance vocale en JavaScript](GetStartedJSWebsockets.md)
* [Bien démarrer avec l’API Reconnaissance vocale à l’aide de REST](GetStartedREST.md)
