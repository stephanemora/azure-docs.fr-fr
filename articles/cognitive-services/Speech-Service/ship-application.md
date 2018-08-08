---
title: Azure Cognitive Services, documentation sur les API du kit de développement logiciel (SDK) Speech de Cognitive Services - Tutoriels, référence API
description: Découvrez comment créer et développer des applications avec le kit de développement logiciel (SDK) Speech de Cognitive Services
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: df6224208e93f4a332247440933b3a4d11c1bb43
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283029"
---
# <a name="shipping-an-application"></a>Expédition d’une application

Respectez la [licence du kit de développement logiciel (SDK) Speech](license.md), ainsi que les [avis concernant les logiciels tiers](third-party-notices.md), lorsque vous distribuez le kit de développement logiciel (SDK) Speech de Cognitive Services. Lisez également la [Déclaration de confidentialité Microsoft](https://aka.ms/csspeech/privacy).

Selon la plateforme, il existe différentes dépendances pour exécuter votre application.

## <a name="windows"></a>Windows

Le kit de développement logiciel (SDK) Speech de Cognitive Services est testé sur Windows 10 et Windows Server 2016.

Le kit de développement logiciel (SDK) Speech de Cognitive Services nécessite que [Microsoft Visual C++ Redistributable pour Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) soit installé sur le système. Vous pouvez télécharger le programme d’installation de la dernière version du `Microsoft Visual C++ Redistributable for Visual Studio 2017` ici :

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Si votre application utilise du code managé, `.Net Framework 4.6.1` ou une version ultérieure est requis sur l’ordinateur cible.

Pour l’entrée du microphone, les bibliothèques Media Foundation doivent être installées. Ces bibliothèques font partie de Windows 10 et de Windows Server 2016. Il est possible d’utiliser le kit de développement logiciel (SDK) Speech sans ces bibliothèques, tant que le microphone n’est pas utilisé comme périphérique d’entrée audio.

Les fichiers du kit de développement logiciel (SDK) Speech requis peuvent être déployés dans le même répertoire que votre application. De cette façon, votre application peut accéder directement aux bibliothèques. Veillez à sélectionner la version appropriée (Win32/x64) correspondant à votre application.

| NOM | Fonction
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Kit de développement logiciel (SDK) principal, requis pour le déploiement natif et managé
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | requis pour le déploiement managé
| `Microsoft.CognitiveServices.Speech.csharp.dll` | requis pour le déploiement managé

## <a name="linux"></a>Linux

Pour une application native, vous devez fournir la bibliothèque du kit de développement logiciel (SDK) Speech, `libMicrosoft.CognitiveServices.Speech.core.so`.
Veillez à sélectionner la version (x86, x64) correspondant à votre application. Selon la version de Linux, vous devrez peut-être également inclure les dépendances suivantes :

* Les bibliothèques partagées de la bibliothèque C de GNU (y compris la bibliothèque de programmation POSIX Threads, `libpthreads`)
* La bibliothèque OpenSSL (`libssl.so.1.0.0`)
* La bibliothèque cURL (`libcurl.so.4`)
* La bibliothèque partagée pour les applications ALSA (`libasound.so.2`)

Sur Ubuntu 16.04, par exemple, les bibliothèques C de GNU doivent déjà être installées par défaut. Les trois dernières peuvent être installées à l’aide de ces commandes :

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
