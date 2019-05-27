---
title: 'Démarrage rapide : Exécuter les appareils de Speech SDK sur Android - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Conditions préalables et les instructions de mise en route avec un kit de développement logiciel Android vocale appareils.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 073166a594088bca04d81883247a5880fcbd1cb7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234526"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Démarrage rapide : Exécuter l’exemple d’application Speech SDK de périphériques sur Android

Dans ce démarrage rapide, vous allez apprendre à utiliser les appareils de Speech SDK pour Android pour la génération d’un produit de reconnaissance vocale ou utilisez-la en tant qu’un [Transcription de Conversation](conversation-transcription-service.md) appareil.

Ce guide nécessite une [Azure Cognitive Services](get-started.md) compte avec une ressource de Services de reconnaissance vocale. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

Le code source de l’exemple d’application est fourni avec le Kit de développement logiciel (SDK) Speech Devices. Il est également [disponible sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer à l’aide du SDK d’appareils vocale, vous devez :

* Suivez les instructions fournies avec votre [kit de développement](get-speech-devices-sdk.md) pour l’appareil sous tension.

* Téléchargez la dernière version de la [Speech SDK de périphériques](https://aka.ms/sdsdk-download)et extrayez le fichier zip dans votre répertoire de travail.
   > [!NOTE]
   > Le fichier Android-exemples-Release.zip inclut l’application exemple Android, et ce démarrage rapide part du principe que l’application est extrait dans C:\SDSDK\Android-Sample-Release

* Pour obtenir un [clé d’abonnement Azure pour les Services de reconnaissance vocale](get-started.md)

* Si vous envisagez d’utiliser la Transcription de Conversation, vous devez utiliser un [DISPOSITIF microphone circulaire](get-speech-devices-sdk.md) et le service est actuellement uniquement disponible pour « en-US » et « zh-CN » dans les régions, « centralus » et « eastasia ». Vous devez disposer d’une clé de la reconnaissance vocale dans une de ces régions à utiliser la Transcription de Conversation.

* Si vous envisagez d’utiliser les Services de reconnaissance vocale pour identifier les intentions (ou les actions) à partir des énoncés d’utilisateur, vous aurez besoin un [Service LUIS (Language Understanding)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) abonnement. Pour en savoir plus sur LUIS et la reconnaissance des intentions, consultez [reconnaître les intentions de reconnaissance vocale avec LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Vous pouvez [créer un modèle LUIS simple](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utiliser l’exemple de modèle LUIS, LUIS-example.json. L’exemple de modèle LUIS est accessible à partir du [site de téléchargement du Kit de développement logiciel (SDK) Speech Devices](https://aka.ms/sdsdk-luis). Pour charger le fichier JSON de votre modèle dans le [portail LUIS](https://www.luis.ai/home), sélectionnez **Importer une nouvelle application**, puis sélectionnez le fichier JSON.

* Installez [Android Studio](https://developer.android.com/studio/) et [Vysor](https://vysor.io/download/) sur votre PC.

## <a name="set-up-the-device"></a>Configurer l’appareil

1. Démarrez Vysor sur votre ordinateur.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Votre appareil doit être répertorié sous **Choose a device** (Choisir un appareil). Sélectionnez le bouton **View** (Afficher) en regard de l’appareil.

1. Connectez-vous à votre réseau sans fil en sélectionnant l’icône de dossier, puis sélectionnez **Settings** (Paramètres) > **WLAN** (WLAN).

    ![WLAN Vysor](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Si votre entreprise applique des stratégies relatives à la connexion d’appareils à son système Wi-Fi, vous devez obtenir l’adresse MAC et contacter votre service informatique pour savoir comment vous connecter au système Wi-Fi de votre entreprise.
    >
    > Pour trouver l’adresse MAC du kit de développement, sélectionnez l’icône de dossier de fichiers sur le bureau du kit de développement.
    >
    >  ![Dossier de fichiers Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Sélectionnez **Paramètres**. Recherchez « mac address », puis sélectionnez **Mac address** (Adresse MAC) > **Advanced WLAN** (WLAN avancé). Notez l’adresse MAC qui s’affiche au bas de la boîte de dialogue.
    >
    > ![Adresse MAC Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Certaines entreprises peuvent appliquer une durée limite pour la connexion d’un appareil à leur système Wi-Fi. Vous devrez peut-être étendre l’inscription du kit de développement auprès de votre système Wi-Fi au bout d’un nombre de jours spécifique.

## <a name="run-the-sample-application"></a>Exécuter l’exemple d’application

Pour valider votre programme d’installation du kit de développement, générer et installer l’exemple d’application :

1. Démarrez Android Studio.

1. Sélectionnez **Ouvrir un projet Android Studio**.

   ![Android Studio : ouverture d’un projet existant](media/speech-devices-sdk/qsg-5.png)

1. Accédez à C:\SDSDK\Android-Sample-Release\example. Sélectionnez **OK** pour ouvrir l’exemple de projet.

1. Ajoutez votre clé d’abonnement vocale au code source. Si vous voulez essayer la reconnaissance de l’intention, ajoutez également votre clé d’abonnement au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), ainsi que l’ID de l’application.

   Pour la reconnaissance vocale et LUIS vos informations prendra MainActivity.java :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Si vous utilisez la transcription de conversation vos informations de clé et la région de reconnaissance vocale sont également nécessaires dans conversation.java :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Le mot signifiant (mot clé) par défaut est « Computer ». Vous pouvez également essayer l’un des autres mots signifiants fournis, tels que « Machine » ou « Assistant ». Les fichiers de ressources pour ces mots alternatifs figurent dans le dossier keyword du Kit de développement logiciel (SDK) Speech Devices. Par exemple, C:\SDSDK\Android-Sample-Release\keyword\Computer contient les fichiers utilisés pour le mot signifiant « Computer ».

   > [!TIP]
   > Vous pouvez également [créer un mot signifiant personnalisé](speech-devices-sdk-create-kws.md).

    Pour utiliser un nouveau mot de mise en éveil, mettre à jour les deux lignes suivantes dans `MainActivity.java`, copiez le package de word de mise en éveil à votre application. Par exemple, pour utiliser le mot de mise en éveil 'Machine' à partir de la kws de package de mise en éveil word-machine.zip :

   * Copiez le package de word de mise en éveil dans le dossier « C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\ ».
   * Mise à jour le `MainActivity.java` avec le mot clé et le nom du package :

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Mettez à jour les lignes ci-après, qui contiennent les paramètres géométriques du réseau de microphones :

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ce tableau répertorie les valeurs prises en charge :

   |Variable|Signification|Valeurs disponibles|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configuration physique du micro|Pour un kit de développement circulaire : `Circular6+1` |
   |||Pour un kit de développement linéaire : `Linear4`|
   |`SelectedGeometry`|Configuration du logiciel du micro|Pour un kit de développement circulaire utilisant tous les micros : `Circular6+1`|
   |||Pour un kit de développement circulaire utilisant quatre micros : `Circular3+1`|
   |||Pour un kit de développement linéaire utilisant tous les micros : `Linear4`|
   |||Pour un kit de développement linéaire utilisant deux micros : `Linear2`|

1. Pour générer l’application, dans le menu **Run** (Exécuter), sélectionnez **Run ’app’** (Exécuter « application »). La boîte de dialogue **Select Deployment Target** (Sélectionner la cible de déploiement) s’affiche.

1. Sélectionnez votre appareil, puis sélectionnez **OK** (OK) pour déployer l’application sur l’appareil.

    ![Boîte de dialogue de sélection de la cible de déploiement](media/speech-devices-sdk/qsg-7.png)

1. L’exemple d’application du Kit de développement logiciel (SDK) Speech Devices démarre et affiche les options suivantes :

   ![Exemple d’application et options du Kit de développement logiciel (SDK) Speech Devices](media/speech-devices-sdk/qsg-8.png)

1. Qui vient d’être ajouté est la démonstration de Transcription de Conversation. Démarrer la transcription avec « Démarrer la Session ». Par défaut, tout le monde est un invité, cependant, si vous avez des signatures de voix du participant elles peuvent être placés dans un /video/participants.properties fichier sur l’appareil. Pour générer la voix signature examiner [transcription des conversations (SDK)](how-to-use-conversation-transcription-service.md).

   ![Application de démonstration Transcription de Conversation](media/speech-devices-sdk/qsg-15.png)

1. Expérimentez !

## <a name="troubleshooting"></a>Résolution de problèmes

   Si vous ne peut pas se connecter à l’appareil de reconnaissance vocale. Tapez la commande suivante dans une fenêtre d’invite de commandes. Elle renvoie une liste des appareils :

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Cette commande utilise Android Debug Bridge, `adb.exe`, qui fait partie de l’installation d’Android Studio. Cet outil figure dans le répertoire C:\Users\[nom de l’utilisateur]\AppData\Local\Android\Sdk\platform-tools. Vous pouvez ajouter ce répertoire à votre chemin d’accès pour que l’invocation de l’outil `adb` soit plus pratique. Dans le cas contraire, vous devez spécifier le chemin d’accès complet à votre installation d’adb.exe dans chaque commande qui invoque `adb`.
   >
   > Si vous voyez une erreur `no devices/emulators found` puis vérifiez votre câble USB est connecté et garantir un câble de haute qualité est utilisé.
   >

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Examiner les notes de publication](devices-sdk-release-notes.md)
