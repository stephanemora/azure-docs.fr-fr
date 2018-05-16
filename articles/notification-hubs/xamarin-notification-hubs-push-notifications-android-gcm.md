---
title: Notifications Push vers des applications Xamarin.Android à l’aide d’Azure Notification Hubs | Microsoft Docs
description: Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Didacticiel : Notifications Push vers des applications Xamarin.Android à l’aide d’Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin.Android. Vous allez créer une application Xamarin.Android vide qui reçoit des notifications Push à l’aide de Firebase Cloud Messaging (FCM). Vous allez utiliser votre hub de notifications pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l’exemple [Application NotificationHubs][GitHub].

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Firebase et activer Firebase Cloud Messaging
> * Créer un hub de notifications
> * Créer une application Xamarin.Android et la connecter au hub de notifications
> * Envoyer des notifications de test à partir du portail Azure

## <a name="prerequisites"></a>Prérequis


- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Visual Studio avec Xamarin] pour Windows ou [Visual Studio pour Mac] sur OS X.
- Un compte Google actif

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Créer un projet Firebase et activer Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Création d’un hub de notifications
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Configurer les paramètres de GCM pour le hub de notifications

1. Sélectionnez **Google (GCM)** dans la section **NOTIFICATION SETTINGS**. 
2. Entrez la **clé du serveur hérité** que vous avez notée à partir de la Google Firebase Console. 
3. Sélectionnez **Save** dans la barre d’outils. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Votre concentrateur de notification est configuré pour FCM, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception de notifications et l’envoi de notifications Push.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Créer une application Xamarin.Android et la connecter au hub de notifications

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Création d’un projet Visual Studio et ajout des packages NuGet
1. Dans Visual Studio, allez dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**. 
   
      ![Visual Studio : Créer un projet Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. Dans la fenêtre **Explorateur de solutions**, développez **Propriétés**, puis cliquez sur **AndroidManifest.xml**. Mettez à jour le nom du package pour correspondre au nom de package que vous avez saisi lors de l’ajout de Firebase Cloud Messaging à votre projet dans la Google Firebase Console. 

      ![Nom du package dans GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Cliquez avec le bouton droit sur votre projet et sélectionnez **Gérer les packages NuGet...**. 
4. Sélectionnez l’onglet **Parcourir**. Recherchez **Xamarin.GooglePlayServices.Base**. Sélectionnez **Xamarin.GooglePlayServices.Base** dans la liste des résultats. Sélectionnez **Installer**. 

      ![Package NuGet de Google Play Services](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. Dans la fenêtre **Gestionnaire de packages NuGet**, recherchez **Xamarin.Firebase.Messaging**. Sélectionnez **Xamarin.Firebase.Messaging** dans la liste des résultats. Sélectionnez **Installer**. 
6. Recherchez maintenant **Xamarin.Azure.NotificationHubs.Android**. Sélectionnez **Xamarin.Azure.NotificationHubs.Android** dans la liste des résultats. Sélectionnez **Installer**. 

### <a name="add-the-google-services-json-file"></a>Ajout du fichier JSON Google Services

1. Copiez dans le dossier du projet le fichier **google-services.json** que vous avez téléchargé à partir de la Google Firebase Console.
2. Ajoutez **google-services.json** au projet.
3. Sélectionnez **google-services.json** dans la fenêtre **Explorateur de solutions**.
4. Dans le volet **Propriétés**, définissez l’action de génération sur **GoogleServicesJson**. Si vous ne voyez pas **GoogleServicesJson**, fermez et redémarrez Visual Studio, rouvrez le projet et réessayez. 

      ![Action de génération GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configuration de hubs de notification dans votre projet

#### <a name="registering-with-firebase-cloud-messaging"></a>Inscription auprès de Firebase Cloud Messaging

Ouvrez le fichier **AndroidManifest.xml** et insérez les éléments `<receiver>` suivants dans l’élément `<application>` :

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Collectez les informations suivantes pour votre application Android et votre concentrateur de notifications :
   
   * **Chaîne de connexion d’écoute** : dans le tableau de bord du [portail Azure], sélectionnez **Afficher les chaînes de connexion**. Copiez la chaîne de connexion *DefaultListenSharedAccessSignature* pour cette valeur.
   * **Nom du hub** : nom de votre hub sur le [portail Azure]. Par exemple, *mynotificationhub2*.
     
2. Cliquez avec le bouton droit sur votre **projet** dans la fenêtre **Explorateur de solutions** et sélectionnez **Ajouter**, puis **Classe**. 
4. Créez une classe **Constants.cs** pour votre projet Xamarin et définissez les valeurs constantes suivantes dans la classe. Remplacez les espaces réservés par vos valeurs.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Ajoutez les instructions using suivantes à **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```
4. Ajoutez une variable d’instance à la classe **MainActivity.cs** qui sera utilisée pour afficher une boîte de dialogue d’alerte lors de l’exécution de l’application :
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Ajoutez le code suivant à `OnCreate` dans **MainActivity.cs** après `base.OnCreate(savedInstanceState)` :

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Créez une nouvelle classe, appelée **MyFirebaseIIDService**, de la même manière que vous avez créé la classe **Constants**. 
8. Ajoutez les instructions using suivantes à **MyFirebaseIIDService.cs** :
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. Dans **MyFirebaseIIDService.cs**, ajoutez la déclaration de **classe** suivante, puis faites en sorte que votre classe hérite des paramètres de **FirebaseInstanceIdService** :
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. Dans **MyFirebaseIIDService.cs**, ajoutez le code suivant :
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Créez une autre classe pour votre projet et nommez-la **MyFirebaseMessagingService**.
12. Ajoutez les instructions using suivantes à la classe **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Ajoutez le code suivant au-dessus de votre déclaration de classe, puis faites en sorte que votre classe hérite des paramètres de **FirebaseMessagingService** :
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Ajoutez le code suivant à la classe **MyFirebaseMessagingService.cs** :
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Générez** votre projet. 
16. **Exécuter** votre application sur votre appareil ou un émulateur chargé

## <a name="send-test-notification-from-the-azure-portal"></a>Envoyer une notification de test à partir du portail Azure
Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* du [portail Azure]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Les notifications Push sont normalement envoyées dans un service backend tel que Mobile Services ou ASP.NET par le biais d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre backend, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous envoyez des notifications de diffusion à tous vos appareils Android inscrits auprès du serveur principal. Pour savoir comment envoyer des notifications Push à des appareils Android, passez au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio avec Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio pour Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[portail Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
