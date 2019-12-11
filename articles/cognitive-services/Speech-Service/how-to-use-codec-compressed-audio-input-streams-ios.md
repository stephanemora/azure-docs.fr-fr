---
title: Streaming du contenu audio compressé par codec avec le SDK Speech sur iOS
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé sur le service Speech avec le SDK Speech sur iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805856"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Activation Utiliser une entrée audio compressée par codec avec le SDK Speech sur iOS

L’API **Compressed Audio Input Stream** du SDK Speech permet de diffuser en streaming du contenu audio compressé sur le service Speech à l’aide d’un flux de tirage (pull) ou d’envoi (push).

> [!IMPORTANT]
> Le kit de développement logiciel (SDK) Speech version 1.7.0 ou ultérieure est requis pour la diffusion en continu de contenu audio compressé sur iOS. Il est également pris en charge pour [C++, C# et Java sur Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) et [Java sur Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Pour wav/PCM, consultez la documentation Speech principale. En dehors de wav/PCM, les formats d’entrées compressées par codec pris en charge sont les suivants :

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav

## <a name="prerequisites"></a>Prérequis

La gestion d'un contenu audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour des raisons de licence, ces fonctions ne peuvent être fournies avec le kit de développement logiciel (SDK). Une bibliothèque wrapper contenant ces fonctions doit être générée par les développeurs d’applications et fournie avec les applications à l'aide du kit de développement logiciel (SDK).

Pour créer cette bibliothèque wrapper, commencez par télécharger et installer le [kit de développement logiciel (SDK) GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Téléchargez ensuite le projet Xcode pour la [bibliothèque wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Ouvrez le projet dans Xcode et générez-le pour la cible **Appareil iOS générique**. Il ne fonctionnera pas pour générer pour une cible spécifique.

L’étape de génération crée un bundle de framework doté d'une bibliothèque dynamique pour toutes les architectures requises portant le nom `GStreamerWrapper.framework`.

Ce framework doit être inclus dans toutes les applications qui utilisent des flux audio compressés avec le SDK du service Speech.

Pour ce faire, appliquez les paramètres suivants à votre projet Xcode :

1. Copiez le `GStreamerWrapper.framework` que vous venez de créer et le framework du kit de développement logiciel (SDK) Cognitive Services Speech, que vous pouvez télécharger [ici](https://aka.ms/csspeech/iosbinary), dans le répertoire contenant votre exemple de projet.
1. Ajustez les chemins des frameworks dans les _paramètres du projet_.
   1. Sous l’onglet **Général** sous l’en-tête **Embedded Binaries** (Binaires incorporés), ajoutez la bibliothèque du SDK comme framework : **Ajouter des binaires incorporées** > **Ajouter d'autres...** > Accédez au répertoire que vous avez choisi et sélectionnez les deux frameworks.
   1. Accédez à l’onglet **Build Settings** (paramètres de Build) et activez tous les paramètres (**All**).
1. Ajoutez le répertoire `$(SRCROOT)/..` aux chemins de recherche de framework (_Framework Search Paths_) sous l’en-tête **Search Paths** (chemins de recherche).

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

Pour diffuser en streaming des données dans un format audio compressé sur le service Speech, créez un élément `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.

L’extrait de code suivant montre comment créer un `SPXAudioConfiguration` à partir d’une instance de `SPXPushAudioInputStream`, en spécifiant MP3 comme format de compression du flux.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

L’extrait de code suivant montre comment les données audio compressées peuvent être lues à partir d’un fichier pour alimenter `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Modalités de la reconnaissance vocale dans Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
