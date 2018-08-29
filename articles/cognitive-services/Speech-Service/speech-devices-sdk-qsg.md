---
title: Prise en main du Kit de développement logiciel (SDK) Speech Devices
description: Composants requis et instructions pour bien démarrer avec le kit de développement logiciel (SDK) Speech Devices.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 463a015b7c01dafc5b30de56b95fa0510ffb98e4
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42424367"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Prise en main du Kit de développement logiciel (SDK) Speech Devices

Cet article décrit comment configurer votre PC de développement et votre kit de développement Speech Devices afin de développer des appareils avec reconnaissance vocale à l’aide du kit de développement logiciel (SDK) Speech Devices. Ensuite, vous créerez et déploierez un exemple d’application pour l’appareil. 

Le code source de l’exemple d'application est inclus avec le kit de développement logiciel (SDK) et est aussi [disponible sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prérequis

Avant de commencer le développement avec le kit de développement logiciel (SDK) Speech Devices, rassemblez les informations et les logiciels dont vous aurez besoin.

* Obtenez un kit de développement [de Roobo](http://ddk.roobo.com/). Les kits sont disponibles avec des configurations de réseau de microphones circulaires ou linéaires. Choisissez celle qui convient à vos besoins.

    |Configuration du kit de développement|Emplacement de l'orateur|
    |-----------------------------|------------|
    |Circulaire|Toutes directions à partir de l’appareil|
    |Linéaire|À l’avant de l’appareil|

* Obtenez la dernière version du kit de développement logiciel (SDK) Speech Devices, comprenant un exemple d’application Android, depuis le [site de téléchargement](https://shares.datatransfer.microsoft.com/) du kit de développement logiciel (SDK) Speech Devices. Décompressez le fichier ZIP dans un dossier local (tel que `C:\SDSDK`).

* Installez [Android Studio](https://developer.android.com/studio/) et [Vysor](http://vysor.io/download/) sur votre PC.

* Obtenez une [clé d’abonnement](get-started.md) du service Speech. Vous pouvez obtenir un essai gratuit de 30 jours, ou une clé depuis votre tableau de bord Azure.

* Si vous voulez utiliser la reconnaissance de l’intention de votre service de reconnaissance vocale, abonnez-vous au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) et [obtenez une clé d’abonnement](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Vous pouvez [créer un modèle LUIS simple](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) ou utilisez l’exemple de modèle LUIS, `LUIS-example.json`, disponible sur le [site de téléchargement](https://shares.datatransfer.microsoft.com/) du kit de développement logiciel (SDK) Speech Services. Chargez le fichier JSON de votre modèle dans le [portail LUIS](https://www.luis.ai/home) en cliquant sur **Importer une nouvelle application** et choisissez le fichier JSON.

## <a name="set-up-the-development-kit"></a>Installer le kit de développement

1. Allumez le kit de développement à l’aide d’un câble mini-USB connecté à un PC ou un adaptateur d’alimentation. Un voyant vert doit s’allumer sous le tableau supérieur.

1. Connectez le kit de développement à un ordinateur à l’aide d’un deuxième câble USB mini.

    ![connexion du kit de développement](media/speech-devices-sdk/qsg-1.png)

1. Orientez correctement votre kit de développement.

    |Configuration du kit de développement|Orientation|
    |-----------------------------|------------|
    |Circulaire|Droit, les microphones vers le haut|
    |Linéaire|Sur le côté, les microphones vers vous (comme montré ci-dessous)|

    ![orientation linéaire du kit de développement](media/speech-devices-sdk/qsg-2.png)

1. Installez les certificats et le fichier de table du mot signifiant (mot clé), puis définissez les autorisations de l’appareil audio. Exécutez les commandes suivantes dans une fenêtre de commande.

    > [!NOTE]
    > Ces commandes utilisent Android Debug Bridge, `adb.exe`, compris dans l’installation d’Android Studio. Cet outil peut être trouvé dans `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Il est recommandé d’ajouter ce répertoire à votre chemin d’accès pour qu’il soit plus pratique d’invoquer `adb`. Autrement, vous devez spécifier le chemin d’accès complet vers votre installation de `adb.exe` dans chaque commande qui invoque `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Coupez le son du microphone et de l’enceinte de votre PC. Ainsi, vous êtes certain de travailler avec les microphones du kit de développement, et vous n’activerez pas l’appareil par accident, à cause du son du PC.
    
1.  Lancez Vysor sur votre ordinateur.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Votre appareil doit être listé sous « Choisir un appareil ». Cliquez sur le bouton **Vue** à côté. 
 
1.  Connectez-vous à votre réseau sans fil en cliquant sur l’icône de dossier, puis **Paramètres**, et **WLAN**.

    ![WLAN Vysor](media/speech-devices-sdk/qsg-4.png)
 
 > [!NOTE]
 > Si votre entreprise possède des stratégies concernant la connexion d’appareils au système Wi-Fi, vous devez obtenir l’adresse Mac et contactez votre service informatique pour savoir comment le connecter à votre système Wi-Fi. Pour trouver l’adresse Mac du kit de développement, cliquez sur l’icône de dossier de fichiers sur le bureau du kit de développement, puis sur **Paramètres**, recherchez « Adresse Mac », cliquez sur **Adresse Mac** pour arriver dans **WLAN avancé**, notez l’adresse Mac trouvée en bas. En outre, certaines entreprises peuvent avoir une durée limite pour la connexion d’un appareil à leurs systèmes Wi-Fi. Vous devrez peut-être étendre l’inscription du kit de développement avec votre système Wi-Fi, après un certain nombre de jours.  
 
 
   ![Dossier de fichiers Vysor](media/speech-devices-sdk/qsg-10.png)
   
   ![Adresse MAC Vysor](media/speech-devices-sdk/qsg-11.png)
   
   
 > Si vous souhaitez raccorder un haut-parleur au kit de développement, vous pouvez le connecter à la sortie de la ligne audio. Vous devez également choisir un haut-parleur de 3,5 mm de bonne qualité.
 
   ![Vysor Audio](media/speech-devices-sdk/qsg-14.png)
 
## <a name="run-a-sample-application"></a>Exécuter un exemple d’application

Pour exécuter les tests Roobo et valider l’installation de votre kit de développement, créez et installez l’exemple d'application.

1.  Lancez Android Studio.

1.  Ouvrez un projet Android Studio existant.

    ![Android Studio : ouvrir un projet existant](media/speech-devices-sdk/qsg-5.png)
 
1.  Accédez à `C:\SDSDK\Android-Sample-Release\example`, puis cliquez sur **OK** pour ouvrir l’exemple de projet.
 
1.  Ajoutez votre clé d’abonnement Speech au code source. Si vous voulez essayer la reconnaissance de l’intention, ajoutez aussi votre clé d’abonnement au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), ainsi que l’ID de l’application. 

    Les informations de vos clés et de votre application sont affichées comme suit dans le fichier source `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Le mot signifiant (mot clé) par défaut est « Computer ».  Si vous le désirez, vous pouvez essayer l’un des autres mots signifiants fournis, « Machine » et « Assistant ». Les fichiers de ressources pour ces mots alternatifs peuvent être trouvés dans le kit de développement Speech Devices, dans le dossier « keyword » (mot clé). Par exemple, `C:\SDSDK\Android-Sample-Release\keyword\Computer` contient les fichiers utilisés pour « Computer ».

    Vous pouvez aussi [créer un mot signifiant personnalisé](speech-devices-sdk-create-kws.md).

    Pour installer le mot signifiant souhaité :
 
    * Créez un dossier `keyword` dans le dossier \data\ sur l’appareil en exécutant les commandes suivantes dans la fenêtre de commande.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Copiez les fichiers `kws.table`, `kws_g.fst`, `kws_k.fst` et `words_kw.txt` dans le dossier \data\keyword\ de l’appareil. Exécutez les commandes suivantes dans une fenêtre de commande. Si vous avez créé un [mot déclencheur personnalisé](speech-devices-sdk-create-kws.md), le fichier kws.table généré à partir du web va se trouver dans le même répertoire que les fichiers `kws.table`, `kws_g.fst`, `kws_k.fst`, et `words_kw.txt`. Utilisez adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table/data/mot clé de commande pour transmettre le fichier kws.table au kit de développement à la place.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Référencez ces fichiers dans l’exemple d'application. Trouvez les lignes suivantes dans `MainActivity.java`. Veillez à ce que le mot clé spécifié est celui que vous utilisez et que le chemin redirige vers le fichier `kws.table` que vous avez envoyé par push à l’appareil.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Dans votre code, vous pouvez utiliser le fichier `kws.table` pour créer une instance de modèle de mot clé et lancer la reconnaissance comme indiqué ci-après.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Mettez à jour les lignes suivantes contenant les paramètres géométriques du réseau de microphone.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Variable|Signification|Valeurs disponibles|
    |--------|-------|----------------|
    |`DeviceGeometry`|Configuration physique du micro|`Circular6+1` pour kit de développement circulaire|
    ||| `Linear4` pour kit de développement linéaire|
    |`SelectedGeometry`|Configuration du logiciel du micro|`Circular6+1` pour kit de développement circulaire utilisant tous les micro|
    |||`Circular3+1` pour kit de développement circulaire utilisant quatre micro|
    |||`Linear4` pour kit de développement linéaire utilisant tous les micro|
    |||`Linear2` pour kit de développement linéaire utilisant deux micro|


1.  Créez l’application en choisissant **Exécuter 'app'** depuis le menu Exécuter. La boîte de dialogue Sélectionner la cible de déploiement s’affiche. Choisissez votre appareil et cliquez sur **OK** pour déployer l’application sur l’appareil.

    ![sélectionner la cible de déploiement](media/speech-devices-sdk/qsg-7.png)
 
1.  L’exemple d’application du kit de développement logiciel (SDK) Speech Devices démarre, affichant les options montrées ici.

    ![exemple d’application speech device](media/speech-devices-sdk/qsg-8.png)

1. Expérimentez !

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des erreurs de certificat lorsque vous utilisez le service de reconnaissance vocale, veillez à ce que la date et l’heure de votre appareil soient correctes. Accédez à **Paramètres**, cliquez sur **Date et heure** sous Système, et **Sélectionnez un fuseau horaire** conforme à votre fuseau horaire actuel. Gardez **Date et heure automatiques** sur ON. Lorsque vous voyez que l’heure du kit de développement correspond à l’heure de l’ordinateur, vous saurez que le kit de développement est connecté à internet. 

 ![Dossier de fichiers Vysor](media/speech-devices-sdk/qsg-12.png)
 
 ![Dossier de fichiers Vysor](media/speech-devices-sdk/qsg-13.png)

Pour en savoir plus sur le développement, consultez le [guide de développement](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) de Roobo.

Il fournit un outil qui capture tout audio en mémoire flash, ce qui peut aider à résoudre des problèmes audio. Une version de cet outil est fournie avec chaque configuration de kit de développement. Choisissez votre appareil sur le [site Roobo](http://ddk.roobo.com/), puis cliquez sur le lien **Outils ROOBO** en bas de la page.
