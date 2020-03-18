---
title: À propos du SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Le kit de développement logiciel (SDK) Speech fournit à vos applications un accès natif aux fonctions du service Speech, ce qui facilite le développement de logiciels. Cet article fournit des détails supplémentaires sur le kit SDK pour Windows, Linux et Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331091"
---
# <a name="about-the-speech-sdk"></a>À propos du kit SDK Speech

Le SDK Speech permet à vos applications d’accéder aux fonctions du service Speech, facilitant le développement de logiciels de reconnaissance vocale. Actuellement, les SDK permettent d’accéder aux options de **reconnaissance vocale**, **synthèse vocale**, **traduction vocale**, **reconnaissance de l’intention** et **canal Direct Line Speech de Bot Framework**.

Vous pouvez facilement capturer du contenu audio à partir d’un microphone, lire un flux ou accéder à des fichiers audio dans un espace de stockage avec le Kit de développement logiciel (SDK) Speech. Le SDK Speech prend en charge le contenu audio monocanal WAV/PCM 16 bits, 16 kHz/8 kHz pour la reconnaissance vocale. D’autres formats audio sont pris en charge en utilisant le [point de terminaison REST de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou le [service de transcription Batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Une vue d’ensemble des fonctionnalités et plateformes prises en charge est disponible sur la [page d’accès](https://aka.ms/csspeech) de la documentation.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtention du Kit de développement logiciel (SDK)

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Le SDK Speech prend en charge Windows 10 ou ultérieur. Les versions antérieures de Windows ne sont **pas prises en charge**.

Pour Windows, nous prenons en charge les langages suivants :

* C# (UWP et .NET), C++ : vous pouvez référencer et utiliser la dernière version de notre package NuGet du kit SDK Speech. Le package inclut des bibliothèques clientes 32 bits et 64 bits, ainsi que des bibliothèques managées (.NET). Vous pouvez installer le Kit de développement logiciel (SDK) dans Visual Studio à l’aide de NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du SDK Speech, qui prend uniquement en charge Windows x64. Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` comme dépendance.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Actuellement, nous prenons uniquement en charge Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 et CentOS 8 sur les architectures cibles suivantes :
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) et ARM64 (Debian/Ubuntu) pour le développement de C++
> - x64, ARM32 (Debian/Ubuntu) et ARM64 (Debian/Ubuntu) pour Java
> - x64 pour .NET Core et Python

Vérifiez que les bibliothèques requises sont installées en exécutant les commandes shell suivantes :

Sur Ubuntu :

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Sur Debian 9 :

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Sur RHEL/CentOS 8 :

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C# : vous pouvez référencer et utiliser la dernière version de notre package NuGet du kit SDK Speech. Pour référencer le SDK, ajoutez la référence de package suivante à votre projet :

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du kit SDK Speech. Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` comme dépendance.

* C++ : téléchargez le SDK sous forme de [package .tar](https://aka.ms/csspeech/linuxbinary) et décompressez les fichiers dans le répertoire de votre choix. Le tableau suivant montre la structure de dossiers du SDK :

  |Path|Description|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Mentions tierces|
  |`include`|Les fichiers d’en-tête pour C et C++|
  |`lib/x64`|Bibliothèque x64 native pour la liaison avec votre application|
  |`lib/x86`|Bibliothèque x86 native pour la liaison avec votre application|

  Pour créer une application, copiez ou déplacez les fichiers binaires (et les bibliothèques) exigés dans votre environnement de développement. Incluez-les dans votre processus de génération en fonction de vos besoins.

# <a name="android"></a>[Android](#tab/android)

Le Kit de développement logiciel (SDK) Java pour Android est empaqueté au format [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires et les autorisations Android requises. Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/` sous la forme d’un package `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Pour utiliser le package dans votre projet Android Studio, effectuez les changements suivants :

* Dans le fichier build.gradle au niveau du projet, ajoutez le texte suivant à la section `repository` :

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Dans le fichier build.gradle au niveau du module, ajoutez le texte suivant à la section `dependencies` :

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
