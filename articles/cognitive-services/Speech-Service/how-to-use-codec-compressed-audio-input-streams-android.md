---
title: Streaming du contenu audio compressé par codec avec le SDK Speech sur Android
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé sur le service Speech avec le SDK Speech sur Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 01135229167dde3784137ab1b06dfc931766a2e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805839"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Activation Utiliser une entrée audio compressée par codec avec le SDK Speech sur Android

L’API **Compressed Audio Input Stream** du SDK Speech permet de diffuser en streaming du contenu audio compressé vers le service Speech à l’aide de PullStream ou PushStream.

> [!IMPORTANT]
> La diffusion en continu de contenu audio d'entrée compressé est actuellement prise en charge pour [C++, C# et Java sous Linux (Ubuntu 16.04, Ubuntu 18.04 et Debian 9)](how-to-use-codec-compressed-audio-input-streams.md). Elle est également prise en charge pour Java sous Android et [Objective-C sous iOS](how-to-use-codec-compressed-audio-input-streams-ios.md).
> La version 1.7.0 ou ultérieure du SDK Speech est nécessaire.

Pour wav/PCM, consultez la documentation Speech principale. En dehors de wav/PCM, les formats d’entrées compressées par codec pris en charge sont les suivants :

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Conditions préalables à l'utilisation d'une entrée audio compressée par codec sous Android

Le contenu audio compressé par codec est implémenté à l'aide de [GStreamer](https://gstreamer.freedesktop.org). Pour des raisons liées à la gestion des licences, les binaires GStreamer ne sont pas compilés avec le SDK. Vous devrez utiliser les binaires prédéfinis pour Android. Pour télécharger les bibliothèques prédéfinies, consultez [Installation pour le développement Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

`libgstreamer_android.so` est obligatoire. Assurez-vous que vos plug-ins GStreamer sont liés dans `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Vous trouverez ci-dessous des exemples de fichiers `Android.mk` et `Application.mk`. Procédez comme suit pour créer l'objet partagé GStreamer : `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Vous pouvez générer `libgstreamer_android.so` à l'aide de la commande suivante sur Ubuntu 16.04 ou 18.04. Les lignes de commande suivantes ont uniquement été testées pour [Gstreamer Android version 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) avec [Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Une fois l'objet partagé (libgstreamer_android.so) créé, le développeur doit le placer dans l'application Android afin qu'il puisse être chargé par le SDK Speech.

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

Pour diffuser en continu des données dans un format audio compressé vers le service Speech, créez un élément `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux en spécifiant le format de compression du flux.

Supposons que vous disposez d’une classe de flux d’entrée appelée `myPullStream` et que vous utilisez OPUS/OGG. Voici à quoi peut ressembler votre code :

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Modalités de la reconnaissance vocale dans Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
