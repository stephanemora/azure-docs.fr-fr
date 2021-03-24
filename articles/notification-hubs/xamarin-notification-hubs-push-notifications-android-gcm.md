---
title: Envoyer des notifications Push vers des applications Xamarin.Android à l’aide d’Azure Notification Hubs | Microsoft Docs
description: Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221032"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutoriel : Envoyer des notifications Push vers des applications Xamarin.Android à l’aide de Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin.Android. Vous allez créer une application Xamarin.Android vide qui reçoit des notifications Push à l’aide de Firebase Cloud Messaging (FCM). Vous allez utiliser votre hub de notifications pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l’exemple [Application NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Firebase et activer Firebase Cloud Messaging
> * Création d’un hub de notifications
> * Créer une application Xamarin.Android et la connecter au hub de notifications
> * Envoyer des notifications de test à partir du portail Azure

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Visual Studio avec Xamarin] pour Windows ou [Visual Studio pour Mac] sur OS X.
* Un compte Google actif

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Créer un projet Firebase et activer Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Création d’un hub de notifications

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Configurer les paramètres GCM/FCM pour le hub de notifications

1. Sélectionnez **Google (GCM/FCM)/** dans la section **Paramètres** du menu de gauche.
2. Entrez la **clé du serveur** que vous avez notée à partir de la console Google Firebase.
3. Sélectionnez **Enregistrer** dans la barre d’outils.

    ![Capture d’écran du hub de notification dans le portail Azure avec l’option Google GCM/FCM encadrée en rouge.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Votre concentrateur de notification est configuré pour FCM, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception de notifications et l’envoi de notifications Push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Créer une application Xamarin.Android et la connecter au hub de notification

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Création d’un projet Visual Studio et ajout des packages NuGet

> [!NOTE]
> Les étapes décrites dans ce tutoriel sont destinées à Visual Studio 2017. 

1. Dans Visual Studio, ouvrez le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**. Dans la fenêtre **Nouveau projet**, procédez comme suit :
    1. Développez **Installé**, **Visual C#** , puis cliquez sur **Android**.
    2. Sélectionnez **Application Android (Xamarin)** dans la liste.
    3. Entrez un **nom** pour le projet.
    4. Sélectionnez un **emplacement** pour le projet.
    5. Sélectionnez **OK**.

        ![Boîte de dialogue Nouveau projet](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Dans la boîte de dialogue **application Android**, sélectionnez **Application vide**, puis **OK**.

    ![Capture d’écran qui met en évidence le modèle Application vide.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Dans la fenêtre **Explorateur de solutions**, développez **Propriétés**, puis cliquez sur **AndroidManifest.xml**. Mettez à jour le nom du package pour correspondre au nom de package que vous avez saisi lors de l’ajout de Firebase Cloud Messaging à votre projet dans la Google Firebase Console.

    ![Nom du package dans GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Définissez la version Android cible du projet sur **Android 10.0** en effectuant les étapes suivantes : 
    1. Cliquez avec le bouton droit sur votre projet et sélectionnez **Propriétés**. 
    1. Pour le champ **Compiler à l’aide de la version d’Android : (framework cible)** , sélectionnez **Android 10.0**. 
    1. Sélectionnez **Oui** dans la boîte de message pour poursuivre la modification du framework cible.
1. Ajoutez au projet les packages NuGet demandés en suivant ces étapes :
    1. Cliquez avec le bouton droit sur votre projet et sélectionnez **Gérer les packages NuGet...** .
    1. Basculez vers l’onglet **Installé**, sélectionnez **Xamarin.Android.Support.Design**, puis sélectionnez **Mettre à jour** dans le volet droit pour actualiser le package avec la dernière version.
    1. Basculez vers l’onglet **Parcourir**. Recherchez **Xamarin.GooglePlayServices.Base**. Sélectionnez **Xamarin.GooglePlayServices.Base** dans la liste des résultats. Sélectionnez **Installer**.

        ![Package NuGet de Google Play Services](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Dans la fenêtre **Gestionnaire de packages NuGet**, recherchez **Xamarin.Firebase.Messaging**. Sélectionnez **Xamarin.Firebase.Messaging** dans la liste des résultats. Sélectionnez **Installer**.
    7. Recherchez maintenant **Xamarin.Azure.NotificationHubs.Android**. Sélectionnez **Xamarin.Azure.NotificationHubs.Android** dans la liste des résultats. Sélectionnez **Installer**.

### <a name="add-the-google-services-json-file"></a>Ajout du fichier JSON Google Services

1. Copiez dans le dossier du projet le fichier `google-services.json` que vous avez téléchargé à partir de la console Google Firebase.
2. Ajoutez `google-services.json` au projet.
3. Sélectionnez `google-services.json` dans la fenêtre de l’**Explorateur de solutions**.
4. Dans le volet **Propriétés**, définissez l’action de génération sur **GoogleServicesJson**. Si vous ne voyez pas **GoogleServicesJson**, fermez et redémarrez Visual Studio, rouvrez le projet et réessayez.

    ![Action de génération GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configuration de hubs de notification dans votre projet

#### <a name="registering-with-firebase-cloud-messaging"></a>Inscription auprès de Firebase Cloud Messaging

1. Si vous effectuez une migration à partir de Google Cloud Messaging vers Firebase, le fichier `AndroidManifest.xml` de votre projet peut contenir une configuration GCM obsolète, ce qui peut entraîner une duplication des notifications. Modifiez le fichier, puis supprimez les lignes suivantes dans la section `<application>`, le cas échéant :

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Ajoutez les instructions suivantes **avant l’élément application**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Collectez les informations suivantes pour votre application Android et votre concentrateur de notifications :

   * **Listen connection string** (Chaîne de connexion d’écoute) : dans le tableau de bord du [Azure portal], sélectionnez **Afficher les chaînes de connexion**. Copiez la chaîne de connexion `DefaultListenSharedAccessSignature` pour cette valeur.
   * **Hub name** : nom de votre hub sur le [Azure portal]. Par exemple, *mynotificationhub2*.
4. Dans la fenêtre de l’**Explorateur de solutions**, cliquez avec le bouton droit sur votre **projet**, sélectionnez **Ajouter**, puis **Classe**.
5. Créez une classe `Constants.cs` pour votre projet Xamarin et définissez les valeurs constantes suivantes dans la classe. Remplacez les espaces réservés par vos valeurs.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Ajoutez les instructions using suivantes à `MainActivity.cs` :

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Ajoutez les propriétés suivantes à la classe MainActivity :

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Ajoutez à votre projet une classe nommée `AzureListener`.
10. Ajoutez les instructions using suivantes à `AzureListener.cs`.

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Ajoutez le code suivant au-dessus de votre déclaration de classe, puis faites en sorte que votre classe hérite de `Java.Lang.Object` et qu’elle implémente `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Ajoutez le code suivant dans la classe `MyFirebaseMessagingService` pour traiter les messages reçus.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Générez** votre projet.
1. **Exécuter** votre application sur votre appareil ou un émulateur chargé

## <a name="send-test-notification-from-the-azure-portal"></a>Envoyer une notification de test à partir du portail Azure

Vous pouvez tester la réception de notifications dans votre application avec l’option **Test Send** du [Azure portal]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Les notifications Push sont normalement envoyées dans un service backend tel que Mobile Services ou ASP.NET par le biais d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre back-end, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez envoyé des notifications de diffusion à tous vos appareils Android inscrits auprès du serveur principal. Pour découvrir comment envoyer des notifications Push à des appareils Android spécifiques, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

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
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio avec Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio pour Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android