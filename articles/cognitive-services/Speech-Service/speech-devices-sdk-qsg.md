---
title: Bien démarrer avec le kit SDK Speech Devices - Services Speech
titleSuffix: Azure Cognitive Services
description: Composants requis et instructions pour bien démarrer avec le kit de développement logiciel (SDK) Speech Devices.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: dec40f7ec21a4a059c6e4a7c75f6378f9757a479
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916845"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Prise en main du Kit de développement logiciel (SDK) Speech Devices

Cet article décrit comment configurer votre PC de développement et le kit de développement Speech Devices afin de développer des appareils avec reconnaissance vocale à l’aide du Kit de développement logiciel (SDK) Speech Devices. Ensuite, vous générerez et déploierez un exemple d’application pour l’appareil.

Le code source de l’exemple d’application est fourni avec le Kit de développement logiciel (SDK) Speech Devices. Il est également [disponible sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer à développer des appareils avec le Kit de développement logiciel (SDK) Speech Devices, rassemblez les informations et les logiciels dont vous avez besoin :

* Obtenez un [kit de développement auprès de ROOBO](https://ddk.roobo.com/). Les kits sont disponibles avec des configurations de réseau de microphones circulaires ou linéaires. Choisissez celle qui convient à vos besoins.

    |Configuration du kit de développement|Emplacement de l'orateur|
    |-----------------------------|------------|
    |Circulaire|Toutes directions à partir de l’appareil|
    |Linéaire|À l’avant de l’appareil|

* Obtenez la dernière version du Kit de développement logiciel (SDK) Speech Devices, comprenant un exemple d’application Android, à partir du [site de téléchargement du Kit de développement logiciel (SDK) Speech Devices](https://shares.datatransfer.microsoft.com/). Extrayez le fichier zip dans un dossier local, tel que C:\SDSDK.

* Installez [Android Studio](https://developer.android.com/studio/) et [Vysor](https://vysor.io/download/) sur votre PC.

* Obtenir un [clé d’abonnement Services de reconnaissance vocale](get-started.md). Vous pouvez bénéficier d’un essai gratuit de 30 jours ou obtenir une clé à partir de votre tableau de bord Azure.

* Si vous souhaitez utiliser la reconnaissance des intentions des Services de reconnaissance vocale, vous abonner à la [Language Understanding service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) et [obtenir une clé d’abonnement](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Vous pouvez [créer un modèle LUIS simple](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utiliser l’exemple de modèle LUIS, LUIS-example.json. L’exemple de modèle LUIS est accessible à partir du [site de téléchargement du Kit de développement logiciel (SDK) Speech Devices](https://shares.datatransfer.microsoft.com/). Pour charger le fichier JSON de votre modèle dans le [portail LUIS](https://www.luis.ai/home), sélectionnez **Importer une nouvelle application**, puis sélectionnez le fichier JSON.

## <a name="set-up-the-development-kit"></a>Installer le kit de développement

1. Le kit de développement comporte deux connecteurs micro USB. Le connecteur de gauche sert à alimenter le kit de développement ; il est désigné par le terme Alimentation sur l'image ci-dessous. Le connecteur de droite permet de contrôler le kit de développement ; il est désigné par le terme Débogage sur l'image.

    ![Connexion du kit de développement](media/speech-devices-sdk/qsg-1.png)

1. Alimentez le kit de développement à l'aide d'un câble micro USB pour connecter le port d'alimentation à un PC ou à un adaptateur secteur. Un voyant vert s'allume sous le tableau supérieur.

1. Pour contrôler le kit de développement, connectez le port de débogage à un ordinateur à l'aide d'un second câble micro USB. Pour des communications fiables, un câble de qualité doit être utilisé.

1. Orientez votre kit de développement pour la configuration circulaire ou linéaire.

    |Configuration du kit de développement|Orientation|
    |-----------------------------|------------|
    |Circulaire|Droit, les microphones vers le haut|
    |Linéaire|Sur le côté, les microphones orientés vers vous (comme illustré ci-après)|

    ![Orientation linéaire du kit de développement](media/speech-devices-sdk/qsg-2.png)

1. Installer les certificats et définissez les autorisations du périphérique audio. Tapez les commandes ci-après dans une fenêtre d’invite de commandes :

   ```
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
    > Si vous rencontrez une erreur `no devices/emulators found`, vérifiez que votre câble USB est bien connecté et qu'il s'agit d'un câble de qualité. Vous pouvez utiliser `adb devices` pour vérifier que votre ordinateur peut communiquer avec le kit de développement car il renverra une liste d'appareils.

    > [!TIP]
    > Désactivez le micro et le haut-parleur de votre PC pour vous assurer que vous travaillez avec les micros du kit de développement. De cette façon, vous n’activerez pas l’appareil par accident à cause du son du PC.

1.  Démarrez Vysor sur votre ordinateur.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Votre appareil doit être répertorié sous **Choose a device** (Choisir un appareil). Sélectionnez le bouton **View** (Afficher) en regard de l’appareil.

1.  Connectez-vous à votre réseau sans fil en sélectionnant l’icône de dossier, puis sélectionnez **Settings** (Paramètres) > **WLAN** (WLAN).

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
    >
    > Si vous souhaitez raccorder un haut-parleur au kit de développement, vous pouvez le connecter à la sortie de la ligne audio. Vous devez choisir un haut-parleur de 3,5 mm de bonne qualité.
    >
    > ![Audio Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Exécuter un exemple d’application

Pour exécuter les tests ROOBO et valider l’installation de votre kit de développement, générez et installez l’exemple d’application :

1. Démarrez Android Studio.

1. Sélectionnez **Ouvrir un projet Android Studio**.

   ![Android Studio : ouverture d’un projet existant](media/speech-devices-sdk/qsg-5.png)

1. Accédez à C:\SDSDK\Android-Sample-Release\example. Sélectionnez **OK** pour ouvrir l’exemple de projet.

1. Ajoutez votre clé d’abonnement Speech au code source. Si vous voulez essayer la reconnaissance de l’intention, ajoutez également votre clé d’abonnement au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), ainsi que l’ID de l’application.

   Vos informations de clé et d’application s’affichent comme suit dans le fichier source MainActivity.java :

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. Le mot signifiant (mot clé) par défaut est « Computer ». Vous pouvez également essayer l’un des autres mots signifiants fournis, tels que « Machine » ou « Assistant ». Les fichiers de ressources pour ces mots alternatifs figurent dans le dossier keyword du Kit de développement logiciel (SDK) Speech Devices. Par exemple, C:\SDSDK\Android-Sample-Release\keyword\Computer contient les fichiers utilisés pour le mot signifiant « Computer ».

    Vous pouvez également [créer un mot signifiant personnalisé](speech-devices-sdk-create-kws.md).

    Pour utiliser un nouveau mot de mise en éveil, mettre à jour les deux lignes suivantes de MainActivity.java et copiez le package de word de mise en éveil dans votre application. Par exemple pour utiliser la mise en éveil word 'Machine' à partir de la kws de package de mise en éveil word-machine.zip :

   * Copiez le package de word de mise en éveil dans le dossier « C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\ ».
   * Mettez à jour le MainActivity.java le mot clé et le nom du package : 
    
     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Mettez à jour les lignes ci-après, qui contiennent les paramètres géométriques du réseau de microphones :

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```
   Le tableau ci-après décrit les valeurs disponibles :

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

1. Expérimentez !

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="certificate-failures"></a>Erreurs de certificat

Si vous obtenez des erreurs de certificat lorsque vous utilisez les Services de reconnaissance vocale, assurez-vous que votre appareil dispose de la date et l’heure :

1. Accédez à **Settings**. Sous **System** (Système), sélectionnez **Date & time** (Date et heure).

    ![Sélection du paramètre de date et d’heure](media/speech-devices-sdk/qsg-12.png)

1. Laissez l’option **Automatic date & time** (Date et heure automatiques) sélectionnée. Sous **Select time zone** (Sélectionner un fuseau horaire), sélectionnez votre fuseau horaire actuel.

    ![Sélection des options de date et de fuseau horaire](media/speech-devices-sdk/qsg-13.png)

    Lorsque vous voyez que l’heure du kit de développement correspond à l’heure de votre PC, le kit de développement est connecté à Internet.

    Pour plus d’informations sur le développement, consultez le [guide de développement de ROOBO](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Audio

ROOBO fournit un outil qui capture toutes les données audio en mémoire flash. Cet outil peut vous aider à résoudre les problèmes audio. Une version de cet outil est fournie avec chaque configuration de kit de développement. Sur le [site ROOBO](https://ddk.roobo.com/), sélectionnez votre appareil, puis sélectionnez le lien **ROOBO Tools** (Outils ROOBO) au bas de la page.
