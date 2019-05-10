---
title: Reconnaissance vocale appareils SDK à puce Roobo Audio Dev Kit v1 - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Conditions préalables et les instructions de mise en route avec le SDK d’appareils vocale, Kit de développement Audio Smart Roobo v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1a13a44d9db8ed029ce798f0bb34944a1a65a7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409050"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Appareil : Kit de développement Audio à puce Roobo

Cet article fournit des informations spécifiques sur l’appareil le Kit de développement Roobo Smart Audio.

## <a name="set-up-the-development-kit"></a>Installer le kit de développement

1. Le kit de développement comporte deux connecteurs micro USB. Le connecteur de gauche sert à alimenter le kit de développement ; il est désigné par le terme Alimentation sur l'image ci-dessous. Le connecteur de droite permet de contrôler le kit de développement ; il est désigné par le terme Débogage sur l'image.

    ![Connexion du kit de développement](media/speech-devices-sdk/qsg-1.png)

1. Alimentez le kit de développement à l'aide d'un câble micro USB pour connecter le port d'alimentation à un PC ou à un adaptateur secteur. Un voyant vert s'allume sous le tableau supérieur.

1. Pour contrôler le kit de développement, connectez le port de débogage sur un ordinateur à l’aide d’un deuxième câble micro USB. Pour des communications fiables, un câble de qualité doit être utilisé.

1. Orientez votre kit de développement pour la configuration circulaire ou linéaire.

    |Configuration du kit de développement|Orientation|
    |-----------------------------|------------|
    |Circulaire|Droit, les microphones vers le haut|
    |Linéaire|Sur le côté, les microphones orientés vers vous (comme illustré ci-après)|

    ![Orientation linéaire du kit de développement](media/speech-devices-sdk/qsg-2.png)

1. Installer les certificats et définissez les autorisations du périphérique audio. Tapez les commandes ci-après dans une fenêtre d’invite de commandes :

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ces commandes utilisent Android Debug Bridge, `adb.exe`, compris dans l’installation d’Android Studio. Cet outil figure dans le répertoire C:\Users\[nom de l’utilisateur]\AppData\Local\Android\Sdk\platform-tools. Vous pouvez ajouter ce répertoire à votre chemin d’accès pour que l’invocation de l’outil `adb` soit plus pratique. Dans le cas contraire, vous devez spécifier le chemin d’accès complet à votre installation d’adb.exe dans chaque commande qui invoque `adb`.
    >
    > Si vous voyez une erreur `no devices/emulators found` puis vérifiez votre câble USB est connecté et qu’il est un câble de haute qualité. Vous pouvez utiliser `adb devices` pour vérifier que votre ordinateur peut communiquer avec le kit de développement car il renverra une liste d'appareils.
    >
    > [!TIP]
    > Désactivez le micro et le haut-parleur de votre PC pour vous assurer que vous travaillez avec les micros du kit de développement. De cette façon, vous n’activerez pas l’appareil par accident à cause du son du PC.

1. Si vous souhaitez raccorder un haut-parleur au kit de développement, vous pouvez le connecter à la sortie de la ligne audio. Vous devez choisir un présentateur de bonne qualité avec un bouchon analogique 3,5 mm.

    ![Audio Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informations sur le développement

Pour plus d’informations développement, consultez le [guide de développement Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Fichier audio

Roobo fournit un outil qui capture toutes les données audio en mémoire flash. Cet outil peut vous aider à résoudre les problèmes audio. Une version de cet outil est fournie avec chaque configuration de kit de développement. Sur le [Roobo site](https://ddk.roobo.com/), sélectionnez votre appareil, puis le **Roobo outils** lien en bas de la page.

## <a name="next-steps"></a>Étapes suivantes

* [Exécutez l’application exemple Android](speech-devices-sdk-android-quickstart.md)
