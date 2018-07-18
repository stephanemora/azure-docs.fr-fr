---
title: À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services | Microsoft Docs
description: Vue d’ensemble des Kits de développement logiciel (SDK) disponibles pour le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370685"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>À propos du Kit de développement logiciel (SDK) Speech de Cognitive Services

Le Kit de développement logiciel (SDK) Speech de Cognitive Services fournit à vos applications un accès natif aux fonctions du service Speech, facilitant le développement de logiciels. Actuellement, le SDK fournit un accès à **Reconnaissance vocale**, **Traduction vocale** et **Reconnaissance de l'intention**.

Le tableau répertorie les systèmes d’exploitation et langages de programmation actuellement pris en charge.

|Système d’exploitation pris en charge|Langage de programmation|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Appareils|Java\*|

\* *Le Kit de développement logiciel (SDK) Java fait partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>Obtenir le SDK Windows

La version Windows du Kit de développement logiciel (SDK) Speech inclut des bibliothèques client C/C++ 32 bits et 64 bits, ainsi que des bibliothèques managées (.NET) pour l’utilisation avec C#. Le Kit de développement logiciel (SDK) peut être installé dans Visual Studio à l’aide de NuGet. Recherchez simplement `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Obtenir le SDK Linux

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

## <a name="get-the-java-sdk"></a>Obtenir le Kit de développement logiciel (SDK) Java

Le Kit de développement logiciel (SDK) Java fait partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-windows.md)
