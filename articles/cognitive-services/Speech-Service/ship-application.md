---
title: Développer des applications avec le SDK Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment déployer une application qui utilise le SDK Speech sur les plateformes prises en charge.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330802"
---
# <a name="ship-an-application"></a>Délivrer une application

Respectez la [licence du SDK Speech](https://aka.ms/csspeech/license201809), ainsi que les [avis concernant les logiciels tiers](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html), quand vous distribuez le SDK Speech d’Azure Cognitive Services. Lisez également la [Déclaration de confidentialité Microsoft](https://aka.ms/csspeech/privacy).

Selon la plateforme, il existe différentes dépendances pour exécuter votre application.

## <a name="windows"></a>Windows

Le kit de développement logiciel (SDK) Speech de Cognitive Services est testé sur Windows 10 et Windows Server 2016.

Le kit de développement logiciel (SDK) Speech de Cognitive Services nécessite que [Microsoft Visual C++ Redistributable pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) soit installé sur le système. Vous pouvez télécharger le programme d’installation de la dernière version du `Microsoft Visual C++ Redistributable for Visual Studio 2019` ici :

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Si votre application utilise du code managé, `.NET Framework 4.6.1` ou une version ultérieure est requis sur l’ordinateur cible.

Pour l’entrée du microphone, les bibliothèques Media Foundation doivent être installées. Ces bibliothèques font partie de Windows 10 et de Windows Server 2016. Il est possible d’utiliser le SDK Speech sans ces bibliothèques, tant qu’un microphone n’est pas utilisé comme périphérique d’entrée audio.

Les fichiers du kit de développement logiciel (SDK) Speech requis peuvent être déployés dans le même répertoire que votre application. De cette façon, votre application peut accéder directement aux bibliothèques. Veillez à sélectionner la version appropriée (Win32/x64) correspondant à votre application.

| Name | Fonction |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK principal, requis pour le déploiement natif et managé |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Requis pour le déploiement managé                      |

> [!NOTE]
> Depuis la version 1.3.0, le fichier `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fourni dans les versions précédentes) n’est plus nécessaire. La fonctionnalité est désormais intégrée dans le kit SDK principal.

> [!NOTE]
> Pour le projet C# Windows Forms App (.NET Framework), vérifiez que les bibliothèques sont incluses dans les paramètres de déploiement de votre projet. Vous pouvez vérifier cela dans `Properties -> Publish Section`. Cliquez sur le bouton `Application Files` et recherchez les bibliothèques correspondantes dans la liste déroulante. Assurez-vous que la valeur est définie sur `Included`. Visual Studio inclut le fichier lorsque le projet est publié/déployé.

## <a name="linux"></a>Linux

Le SDK Speech prend actuellement en charge les distributions Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8 et CentOS 8.
Pour une application native, vous devez fournir la bibliothèque du SDK Speech, `libMicrosoft.CognitiveServices.Speech.core.so`.
Veillez à sélectionner la version (x86, x64) correspondant à votre application. En fonction de la version de Linux, vous devrez peut-être également inclure les dépendances suivantes :

- Les bibliothèques partagées de la bibliothèque C de GNU (notamment la bibliothèque de programmation POSIX Threads, `libpthreads`)
- La bibliothèque OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
- La bibliothèque partagée pour les applications ALSA (`libasound.so.2`)

Sous Ubuntu, les bibliothèques C de GNU doivent déjà être installées par défaut. Les trois dernières peuvent être installées à l’aide de ces commandes :

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Sur Debian 9, installez ces packages :

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

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Découvrir comment utiliser la reconnaissance vocale en C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
