---
title: À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services
description: Vue d’ensemble des Kits de développement logiciel (SDK) disponibles pour le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: 169d421ddccf33ac239b69ab78ca7dca0f0b8261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958411"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services

Le Kit de développement logiciel (SDK) Speech de Cognitive Services fournit à vos applications un accès natif aux fonctions du service Speech, facilitant le développement de logiciels. Actuellement, le SDK fournit un accès à **Reconnaissance vocale**, **Traduction vocale** et **Reconnaissance de l'intention**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtention du Kit de développement logiciel (SDK)

### <a name="windows"></a>Windows

Pour Windows, nous prenons en charge les langages suivants :

* C# (UWP et .NET), C++ : vous pouvez référencer et utiliser la dernière version de notre package NuGet du Kit de développement logiciel (SDK) de reconnaissance vocale.
  Le package inclut des bibliothèques clientes 32 bits et 64 bits, ainsi que des bibliothèques managées (.NET).
  Le Kit de développement logiciel (SDK) peut être installé dans Visual Studio à l’aide de NuGet. Recherchez simplement `Microsoft.CognitiveServices.Speech`.

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du Kit de développement logiciel (SDK) de reconnaissance vocale, qui prend en charge Windows x64.
  Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0` comme dépendance. 

### <a name="linux"></a>Linux

> [!NOTE]
> Actuellement, nous prenons uniquement en charge Ubuntu 16.04 sur un PC (x86 ou x64 pour le développement C++, x64 pour .NET Core et Java).

Vérifiez que le compilateur et les bibliothèques requis sont installés en exécutant les commandes shell suivantes :

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C# : vous pouvez référencer et utiliser la dernière version de notre package NuGet du Kit de développement logiciel (SDK) de reconnaissance vocale.
  Pour référencer le Kit de développement logiciel (SDK), ajoutez la référence de package suivante à votre projet :

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.0.0" />
  ```

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du Kit de développement logiciel (SDK) de reconnaissance vocale.
  Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0` comme dépendance. 

* C++ : téléchargez le SDK en tant que [package .tar](https://aka.ms/csspeech/linuxbinary) et décompressez les fichiers dans un répertoire de votre choix. Le tableau suivant montre la structure de dossiers du Kit de développement logiciel.

  |path|Description|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Mentions tierces|
  |`include`|Les fichiers d’en-tête pour C et C++|
  |`lib/x64`|Bibliothèque x64 native pour la liaison avec votre application|
  |`lib/x86`|Bibliothèque x86 native pour la liaison avec votre application|

  Pour créer une application, copiez ou déplacez les fichiers binaires (et les bibliothèques) requis dans votre environnement de développement et incluez-les dans votre processus de génération en fonction de vos besoins.

### <a name="android"></a>Android

Le Kit de développement logiciel (SDK) Java pour Android est empaqueté sous forme de package [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires ainsi que les autorisations Android requises pour son utilisation.
Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/`, en tant que package `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`.
Pour utiliser le package dans votre projet Android Studio, effectuez les modifications suivantes :

* Dans le fichier `build.gradle` au niveau du projet, ajoutez le texte suivant à la section `repository` :

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Dans le fichier `build.gradle` au niveau du module, ajoutez le texte suivant à la section `dependencies` :

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.0.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
