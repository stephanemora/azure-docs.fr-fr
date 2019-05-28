---
title: Notifications Push vers des applications Kindle à l’aide d’Azure Notification Hubs | Microsoft Docs
description: Ce didacticiel montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926764"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Prendre en main Notification Hubs pour les applications Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Kindle. Dans ce didacticiel, vous créez une application Kindle vierge recevant des notifications Push à l’aide d’Amazon Device Messaging (ADM).

Dans ce didacticiel, vous créez/mettez à jour le code pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Ajout d'une nouvelle application au portail des développeurs
> * Création d'une clé API
> * Créer et configurer un hub de notification
> * Configuration de votre application
> * Création de votre gestionnaire de messages ADM
> * Ajout de la clé API à votre application
> * Exécution de l'application
> * Envoyer une notification de test

## <a name="prerequisites"></a>Prérequis

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Suivez les étapes de [Configuration de votre environnement de développement](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) pour configurer votre environnement de développement pour Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Ajout d'une nouvelle application au portail des développeurs

1. Connectez-vous au [portail Amazon Developer](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Sélectionnez **Add New App** (Ajouter une application), puis sélectionnez **Android**.  

    ![Bouton Add New App (Ajouter une application)](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Dans la page **New App Submission** (Nouvel envoi d’application), effectuez les étapes suivantes pour obtenir la **clé d’application** :
    1. Entrez un nom dans le champ **App title** (Titre de l’application).
    2. Dans le champ **App category** (Catégorie de l’application), sélectionnez une catégorie (par exemple : éducation)
    4. Entrez une adresse e-mail dans le champ **Customer support email address** (Adresse e-mail du support client). 
    5. Sélectionnez **Enregistrer**.

        ![Page New App Submission (Ajouter une application)](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  En haut de la page, basculez vers l’onglet **Mobile Ads** (Annonces mobiles), puis effectuez les étapes suivantes : 
    1. Précisez si votre application s’adresse principalement aux enfants de moins de 13 ans. Pour les besoins de ce tutoriel, sélectionnez **No** (Non).
    2. Sélectionnez **Envoyer**. 

        ![Page Mobile Ads (Annonces mobiles)](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Copiez la **clé d’application** qui s’affiche dans la page **Mobile Ads** (Annonces mobiles). 

        ![Clé de l'application](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Sélectionnez le menu **Apps & Services** (Applications et services) situé en haut de la page, puis sélectionnez votre application dans la liste. 

    ![Sélection de votre application dans la liste](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Basculez vers l’onglet **Device Messaging**, puis effectuez les étapes suivantes : 
    1. Sélectionnez **Create a New Security Profile** (Créer un profil de sécurité).
    2. Entrez un **nom** pour le profil de sécurité. 
    3. Entrez une **description** pour le profil de sécurité. 
    4. Sélectionnez **Enregistrer**. 
    5. Sélectionnez **View Security Profile** (Afficher le profil de sécurité) dans la page de résultats. 
5. Maintenant, dans la page **Security Profile** (Profil de sécurité), effectuez les étapes suivantes : 
    1. Basculez vers l’onglet **Web Settings** (Paramètres Web), puis copiez les valeurs des champs **Client ID** (ID client) et **Client Secret** (Secret client), car nous en aurons besoin plus tard. 

        ![Récupération de l’lD client et du secret client](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Basculez vers la page **Android/Kindle Settings** (Paramètres Android/Kindle) et laissez-la ouverte. Vous entrerez ces valeurs dans la section suivante. 

## <a name="create-an-api-key"></a>Création d'une clé API
1. Ouvrez une invite de commandes avec les privilèges Administrateur.
2. Accédez au dossier du Kit de développement logiciel (SDK) Android
3. Entrez la commande suivante :

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Pour le mot de passe **keystore**, tapez **android**.
5. Copiez les empreintes digitales **MD5** et **SHA256**. 
6. De retour dans le portail des développeurs, sous l’onglet **Android/Kindle Settings**, effectuez les étapes suivantes : 
    1. Entrez un **nom pour la clé API**. 
    2. Entrez le **nom de package** de votre application (par exemple, **com.fabrikam.mykindleapp**) ainsi que la valeur **MD5**.
        
        >[!IMPORTANT]
        > Lorsque vous créez une application dans Android Studio, utilisez le même nom de package que celui spécifié ici. 
    1. Collez la **signature MD5** que vous avez copiée précédemment. 
    2. Collez la **signature SHA256** que vous avez copiée précédemment.  
    3. Sélectionnez **Generate New Key** (Générer une clé).

        ![Paramètres Android/Kindle - Génération d’une clé](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. À présent, sélectionnez **Show** (Afficher) dans la liste pour voir la clé API. 

        ![Paramètres Android/Kindle - Afficher la clé API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. Dans la fenêtre **API Key Details** (Informations sur la clé API), copiez la clé API, puis enregistrez-la quelque part. Ensuite, sélectionnez **X** dans l’angle droit pour fermer la fenêtre. 


## <a name="create-and-configure-a-notification-hub"></a>Créer et configurer un hub de notification

1. Pour créer un hub de notification, suivez les étapes indiquées dans l’article [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 
2. Sélectionnez **Amazon (ADM)** dans le menu **Paramètres**.
3. Collez l’**ID client** et le **secret client** que vous avez enregistrés précédemment. 
4. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer les paramètres ADM pour le hub de notification](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Dans le menu de gauche, sélectionnez **Stratégies d’accès**, puis sélectionnez le bouton **Copier** pour copier la chaîne de connexion de la stratégie **DefaultListenSharedAccessSignature**. Enregistrez-la à l’emplacement de votre choix. Vous l’utiliserez plus loin dans le code source. 

    ![Hub de notification - Chaîne de connexion d’écoute](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Configuration de votre application

1. Lancez Android Studio. 
2. Sélectionnez **File** (Fichier), pointez sur **New** (Nouveau), puis sélectionnez **New Project** (Nouveau projet). 
3. Dans la page **Choose your project** (Choisir votre projet), sous l’onglet **Phone and Tablet** (Téléphone et tablette), sélectionnez **Empty Activity** (Activité vide), puis sélectionnez **Next** (Suivant). 
4. Dans la fenêtre **Configure your project** (Configurer votre projet), effectuez les étapes suivantes :
    1. Entrez un **nom pour votre application**. Vous pouvez utiliser le même nom que celui que vous avez utilisé pour l’application que vous avez créée dans le portail Amazon Developer. 
    2. Entrez un **nom pour le package**. 
        
        >[!IMPORTANT]
        >Le nom du package doit correspondre à celui que vous avez spécifié dans le portail Amazon Developer.
    3. Passez en revue les valeurs restantes, et mettez-les à jour si besoin. 
    4. Sélectionnez **Terminer**. 

        ![Configurer le projet Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Téléchargez la bibliothèque du [SDK Amazon Developer pour Android](https://developer.amazon.com/sdk-download) sur votre disque dur. Extrayez le fichier zip du Kit de développement logiciel (SDK).
6. Dans Android Studio, remplacez la structure de dossiers **Android** par une structure de type **Project**, si celle-ci n’est pas déjà définie sur **Project**. 

    ![Android Studio - Sélection de la structure Project](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Développez **app** pour afficher le dossier **libs** dans l’arborescence.     
8. Dans l’Explorateur de fichiers, accédez au dossier dans lequel vous avez téléchargé les SDK Android Amazon.
9. Appuyez sur **CTRL**, puis glissez-déposez le fichier **amazon-device-messagerie-1.0.1.jar** vers le nœud **lib** dans l’arborescence. 

    ![Android Studio - Ajout du fichier JAR Amazon Device Messaging](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. Dans la fenêtre **Copy** (Copier), sélectionnez **OK**. Si vous voyez la fenêtre **Move** (Déplacer) au lieu de la fenêtre **Copy** (Copier), fermez-la, puis réessayez l’opération de glisser-déplacer en maintenant le bouton **CTRL** enfoncé. 

    ![Android Studio - Copie du fichier JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Ajoutez l’instruction suivante au fichier **build.gradle de l’application** dans la section **dependencies** : `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio - Ajout d’ADM au fichier build.gradle de l’application](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. Dans le fichier `Build.Gradle` de **app**, sous la section **dependencies**, ajoutez les lignes suivantes : 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Ajoutez le référentiel suivant **après** la section **dependencies** :

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. Dans la barre d’outils de l’éditeur, sélectionnez **Sync now** (Synchroniser maintenant) pour le fichier **build.gradle** de **app**. 

    ![Android Studio - Synchronisation du fichier build.gradle de l’application](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Revenez à la structure Android dans l’arborescence.  Ajoutez l'espace de noms Amazon dans l'élément du manifeste racine :

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Espace de noms Amazon dans le manifeste](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Ajoutez les autorisations comme le premier élément sous l'élément du manifeste. Remplacez **[VOTRE NOM DE PACKAGE]** par le package utilisé pour créer votre application.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Insérez l'élément suivant comme le premier enfant de l'élément de l'application. Remplacez **[VOTRE NOM DE PACKAGE]** par le nom de package utilisé pour créer votre application. Vous créerez la classe MyADMMessageHandler à l’étape suivante. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Création de votre gestionnaire de messages ADM

1. Ajoutez une classe au package `com.fabrikam.mykindleapp` dans le projet qui hérite de `com.amazon.device.messaging.ADMMessageHandlerBase`, puis nommez-la `MyADMMessageHandler`, comme indiqué dans l’image suivante :

    ![Créer la classe MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Ajoutez les instructions `import` suivantes à la classe `MyADMMessageHandler` :

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Ajoutez le code suivant dans la classe que vous avez créée. N’oubliez pas `[HUB NAME]` et `[LISTEN CONNECTION STRING]` avec le nom du hub de notification et la chaîne de connexion d’écoute : 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Ajout de la clé API à votre application
1. Suivez ces étapes pour ajouter un dossier de ressources au projet. 
    1. Basculez vers la vue **Project** (Projet). 
    2. Cliquer avec le bouton droit sur **app**.
    3. Sélectionnez **Nouveau**.
    4. Sélectionnez **Folder** (Dossier). 
    5. Ensuite, sélectionnez **Assets Folder** (Dossier de ressources). 

        ![Ajout du menu Assets Folder (Dossier de ressources)](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Dans la page **Configure Component** (Configurer le composant), effectuez les étapes suivantes :
        1. Sélectionnez **Change folder location** (Modifier l’emplacement du dossier).
        2. Vérifiez que le dossier est défini sur `src/main/assets`.
        3. Sélectionnez **Terminer**. 
        
            ![Configuration du dossier de ressources](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Ajoutez un fichier nommé **api_key.txt** dans le dossier **assets**. Dans l’arborescence, développez **app**, développez **src**, développez **main**, puis cliquez avec le bouton droit sur **assets**. Pointez sur **New** (Nouveau), puis sélectionnez **File** (Fichier). Entrez **api_key.txt** pour le nom de fichier. 3. 
5. Dans le fichier api_key.txt, copiez la clé API que vous avez générée dans le portail Amazon Developer. 
6. Créez le projet. 

## <a name="run-the-app"></a>Exécution de l'application
1. Sur l’appareil Kindle, balayez l’écran à partir du haut et cliquez sur **Settings** (Paramètres), puis sur **My account** (Mon compte), et inscrivez-vous avec un compte Amazon valide.
2. Exécutez l’application sur un appareil Kindle à partir d’Android Studio. 

> [!NOTE]
> Si un problème se produit, vérifiez l'heure de l'émulateur (ou de l'appareil). L'heure doit être exacte. Pour modifier l'heure de l'émulateur Kindle, vous pouvez exécuter la commande suivante depuis le répertoire des outils de plateforme du Kit de développement logiciel (SDK) Android :

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Envoi d’un message de notification

Pour envoyer un message avec .NET :

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Pour obtenir un exemple de code, consultez [cet exemple sur GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous envoyez des notifications de diffusion à tous vos appareils Kindle inscrits avec le serveur principal. Pour savoir comment envoyer des notifications à des appareils Kindle spécifiques, passez au tutoriel suivant :  Le tutoriel suivant montre comment envoyer des notifications vers des appareils Android spécifiques, mais vous pouvez utiliser la même logique pour envoyer des notifications vers des appareils Kindle spécifiques.

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
