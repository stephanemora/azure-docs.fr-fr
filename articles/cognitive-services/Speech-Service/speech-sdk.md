---
title: À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services
description: Vue d’ensemble des Kits de développement logiciel (SDK) disponibles pour le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: c7eaa2aa37b05bd0e125e1841357979af4f6763a
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326057"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services

Le Kit de développement logiciel (SDK) Speech de Cognitive Services fournit à vos applications un accès natif aux fonctions du service Speech, facilitant le développement de logiciels. Actuellement, le SDK fournit un accès à **Reconnaissance vocale**, **Traduction vocale** et **Reconnaissance de l'intention**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtention du Kit de développement logiciel (SDK)

### <a name="get-the-windows-sdk"></a>Obtenir le SDK Windows

La version Windows du Kit de développement logiciel (SDK) Speech inclut des bibliothèques client C/C++ 32 bits et 64 bits, ainsi que des bibliothèques managées (.NET) pour l’utilisation avec C#. Le Kit de développement logiciel (SDK) peut être installé dans Visual Studio à l’aide de NuGet. Recherchez simplement `Microsoft.CognitiveServices.Speech`.

### <a name="get-the-linux-sdk"></a>Obtenir le SDK Linux

Vérifiez que vous disposez du compilateur et des bibliothèques requis en exécutant les commandes shell suivantes :

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Ces instructions supposent que vous exécutez Ubuntu 16.04 sur un PC (x86 ou x64). Sur une autre version Ubuntu, ou une autre distribution Linux, adaptez ces étapes à votre environnement.

Ensuite, [téléchargez le SDK](https://aka.ms/csspeech/linuxbinary) et décompressez les fichiers dans un répertoire de votre choix. Ce tableau présente la structure de dossiers du Kit de développement logiciel.

|path|Description|
|-|-|
|`license.md`|Licence|
|`third-party-notices.md`|Mentions tierces|
|`include`|Les fichiers d’en-tête pour C et C++|
|`lib/x64`|Bibliothèque x64 native pour la liaison avec votre application|
|`lib/x86`|Bibliothèque x86 native pour la liaison avec votre application|

Pour créer une application, copiez ou déplacez les fichiers binaires (et les bibliothèques) requis dans votre environnement de développement et incluez-les dans votre processus de génération en fonction de vos besoins.

### <a name="get-the-java-sdk"></a>Obtenir le Kit de développement logiciel (SDK) Java

Le Kit de développement logiciel (SDK) Java pour Android est empaqueté sous forme de package [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires ainsi que les autorisations Android requises pour son utilisation.
Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/`, en tant que package `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
Pour utiliser le package dans votre projet Android Studio, effectuez les modifications suivantes :

* Dans le fichier `build.gradle` au niveau du projet, ajoutez le texte suivant à la section `repository` :

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Dans le fichier `build.gradle` au niveau du module, ajoutez le texte suivant à la section `dependencies` :

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
