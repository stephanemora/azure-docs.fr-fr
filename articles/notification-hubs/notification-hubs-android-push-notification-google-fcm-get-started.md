---
title: Notifications Push vers des applications Android avec Azure Notification Hubs et Firebase Cloud Messaging | Microsoft Docs
description: Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs et Google Firebase Cloud Messaging pour envoyer des notifications Push à des appareils Android.
services: notification-hubs
documentationcenter: android
keywords: notifications push,notification push,notification push android,fcm,firebase cloud messaging
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 2bc085989ff3bbbc50042c46b338f748a10aa87e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232792"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Didacticiel : notifications Push vers des appareils Android à l’aide de Azure Notification Hubs et de Google Firebase Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!IMPORTANT]
> Cet article explique les notifications Push avec Google Firebase Cloud Messaging (FCM). Si vous utilisez encore Google Cloud Messaging (GCM), consultez [Notifications Push vers des appareils Android avec Azure Notification Hubs et GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).

Ce didacticiel montre comment utiliser Azure Notification Hubs et Cloud Messaging pour envoyer des notifications Push vers une application Android. Dans ce didacticiel, vous allez créer une application Android vide qui reçoit des notifications Push à l’aide de Firebase Cloud Messaging (FCM).

Le code complet de ce didacticiel peut être téléchargé depuis GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Android Studio.
> * Créer un projet qui prend en charge Firebase Cloud Messaging.
> * Créer un hub de notification.
> * Connecter votre application au hub de notification.
> * Tester l'application. 

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/free/).

* En plus du compte Azure actif mentionné ci-dessus, ce didacticiel requiert la dernière version d’ [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 ou ultérieure pour Firebase Cloud Messaging.
* La révision 27 ou ultérieure du référentiel Google est requise pour Firebase Cloud Messaging.
* Services Google Play 9.0.2 ou version ultérieure pour Firebase Cloud Messaging.
* Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android.

## <a name="create-an-android-studio-project"></a>Créer un projet Android Studio
1. Dans Android Studio, démarrez un nouveau projet Android Studio.
   
    ![Android Studio - nouveau projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Choisissez le format **Phone and Tablet** (Téléphone et tablette) et le **Minimum SDK** (SDK minimal) que vous voulez prendre en charge. Cliquez ensuite sur **Suivant**.
   
    ![Android Studio - workflow de création de projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Choisissez **Empty Activity** (Activité vide) comme activité principale, cliquez sur **Next** (Suivant), puis sur **Finish** (Terminer).

## <a name="create-a-firebase-project-that-supports-fcm"></a>Créer un projet qui prend en charge FCM
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurer un hub de notification
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurer les paramètres de Firebase Cloud Messaging pour le hub
1. Sélectionnez **Google (GCM)** dans la catégorie **PARAMÈTRES DE NOTIFICATION**. 
2. Entrez la clé API (clé du serveur FCM) que vous avez précédemment copiée à partir de la [console Firebase](https://firebase.google.com/console/).
3. Sélectionnez **Enregistrer** dans la barre d’outils.

    ![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Votre hub de notification est à présent configuré pour fonctionner avec Firebase Cloud Messaging, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception et l’envoi de notifications Push.

## <a id="connecting-app"></a>Connexion de votre application au hub de notification
### <a name="add-google-play-services-to-the-project"></a>Ajout de services Google Play au projet
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Ajout de bibliothèques de concentrateurs de notification Azure
1. Dans le fichier `Build.Gradle` de **l’application**, dans la section **dépendances**, ajoutez les lignes suivantes.
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Ajoutez le référentiel suivant après la section **dépendances** .
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="add-google-firebase-support"></a>Ajouter la prise en charge de Google Firebase

1. Dans le fichier `Build.Gradle` de **l’application**, dans la section **dépendances**, ajoutez les lignes suivantes.
   
    ```java
        compile 'com.google.firebase:firebase-core:12.0.0'
    ```

2. Ajoutez le plug-in suivant à la fin du fichier. 
   
    ```java
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Mise à jour du fichier AndroidManifest.xml.
1. Pour prendre en charge FCM, vous devez implémenter un service d’écoute d’ID d’instance dans votre code afin [d’obtenir des jetons d’inscription](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) à l’aide de [l’API FirebaseInstanceId Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). Dans ce didacticiel, le nom de la classe est `MyInstanceIDService`. 
   
    Ajoutez la définition de service suivante au fichier AndroidManifest.xml, dans la balise `<application>` . 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. Une fois que vous avez reçu votre jeton d’inscription FCM de la part de l’API FirebaseInstanceId, vous l’utilisez pour vous [inscrire auprès d’Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Vous effectuez cette inscription en arrière-plan à l'aide d'un élément `IntentService` nommé `RegistrationIntentService`. Ce service gère également l’actualisation de votre jeton d’inscription FCM.
   
    Ajoutez la définition de service suivante au fichier AndroidManifest.xml, dans la balise `<application>` . 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. Vous devez également définir le destinataire qui recevra les notifications. Ajoutez la définition de destinataire suivante au fichier AndroidManifest.xml, dans la balise `<application>` . Remplacez l’espace réservé `<your package>` par le nom de votre package actuel, qui apparaît en haut du fichier `AndroidManifest.xml`.

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. Ajoutez les autorisations nécessaires liées à FCM sous la balise `</application>`. 
   
    Pour plus d’informations sur ces autorisations, consultez [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configuration d’une application cliente GCM pour Android) et [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Migration d’une application cliente GCM pour Android vers Firebase Cloud Messaging).
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Ajout de code
1. Dans la vue du projet, développez **app** > **src** > **main** > **java**. Cliquez avec le bouton droit sur le dossier de votre package sous **java**, cliquez sur **Nouveau**, puis sur **Classe Java**. Ajoutez une nouvelle classe nommée `NotificationSettings`. 
   
    ![Android Studio - nouvelle classe Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Veillez à mettre à jour ces trois espaces réservés dans le code suivant pour la classe `NotificationSettings` :
   
   * **SenderId** : ID d’expéditeur obtenu précédemment sous l’onglet **Cloud Messaging** des paramètres de votre projet dans la [console Firebase](https://firebase.google.com/console/).
   * **HubListenConnectionString** : chaîne de connexion **DefaultListenAccessSignature** de votre hub. Vous pouvez copier cette chaîne de connexion en cliquant sur **Stratégies d’accès** dans votre hub sur le [portail Azure].
   * **HubName** : utilisez le nom de votre hub de notification qui s’affiche dans la page Hub sur le [portail Azure].
     
     `NotificationSettings` code :
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. À l’aide de la procédure ci-dessus, ajoutez une autre nouvelle classe nommée `MyInstanceIDService`. Cette classe est votre implémentation de service d’écouteur d’ID d’instance.
   
    Le code de cette classe appelle votre `IntentService` pour [actualiser le jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en arrière-plan.
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. Ajoutez à votre projet une autre nouvelle classe nommée `RegistrationIntentService`. Cette classe implémentent votre interface `IntentService` qui gère [l’actualisation du jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) et [l’inscription au hub de notification](notification-hubs-push-notification-registration-management.md).
   
    Utilisez le code suivant pour cette classe.
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. Dans votre classe `MainActivity`, ajoutez les instructions `import` suivantes au-dessus de la déclaration de la classe.
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```
3. Ajoutez les membres privés suivants dans la partie supérieure de la classe. Vous utilisez ces champs pour [vérifier la disponibilité des Google Play Services comme recommandé par Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. Dans votre classe `MainActivity` , ajoutez la méthode suivante à la disponibilité des services Google Play. 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. Dans votre classe `MainActivity`, ajoutez le code suivant pour rechercher les Google Play Services avant d’appeler votre `IntentService` pour obtenir votre jeton d’inscription FCM et vous inscrire auprès de votre hub de notification.
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. Dans la méthode `OnCreate` de la classe `MainActivity`, ajoutez le code suivant pour lancer le processus d’inscription lorsque l’activité est créée.
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. Ajoutez ces méthodes supplémentaires à `MainActivity` pour vérifier l’état de l’application et afficher un rapport dans votre application.
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. La méthode `ToastNotify` utilise le contrôle *« Hello World »* `TextView` pour afficher en permanence un rapport d’état et des notifications dans l’application. Dans votre disposition activity_main.xml, ajoutez l’ID suivant pour ce contrôle.
   
    ```java
       android:id="@+id/text_hello"
    ```

9. Vous ajoutez ensuite une sous-classe pour votre destinataire que vous avez défini dans le fichier AndroidManifest.xml. Ajoutez à votre projet une autre nouvelle classe nommée `MyHandler`.
10. Ajoutez les instructions d’importation suivantes au-dessus de `MyHandler.java` :
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. Ajoutez le code suivant pour la classe `MyHandler` afin d’en faire une sous-classe de `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Comme ce code remplace la méthode `OnReceive`, le gestionnaire rapporte les notifications reçues. Le gestionnaire envoie également la notification Push au gestionnaire de notifications Android en utilisant la méthode `sendNotification()` . La méthode `sendNotification()` doit être exécutée quand l’application n’est pas en cours d’exécution et qu’une notification est reçue.
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. Dans Android Studio, dans la barre de menus, cliquez sur **Build** (Générer) > **Rebuild Project** (Régénérer le projet) pour vérifier que votre code ne contient aucune erreur.
13. Exécutez l’application sur votre appareil et vérifiez qu’il s’inscrit correctement auprès du hub de notification. 
    
    > [!NOTE]
    > L’inscription peut échouer lors du démarrage initial jusqu’à ce que la méthode `onTokenRefresh()` du service d’ID d’instance soit appelé. L’actualisation doit initier une inscription réussie auprès du hub de notification.
    > 
    > 

## <a name="test-the-app"></a>Test de l'application
### <a name="test-send-notification-from-the-notification-hub"></a>Test de l’envoi d’une notification à partir du hub de notification
Vous pouvez envoyer des notifications Push à partir du [portail Azure] en effectuant les actions suivantes : 

1. Sélectionnez **Envoi de test** dans la section **Résolution des problèmes**.
2. Pour les **plateformes**, sélectionnez **Android**. 
3. Sélectionnez **Envoyer**.  Vous ne voyez pas encore une notification sur l’appareil Android, car vous n’avez pas exécuté l’application mobile dessus. Après avoir exécuté l’application mobile, sélectionnez de nouveau le bouton **Envoyer** pour voir le message de notification. 
4. Consultez le **résultat** de l’opération dans la liste en bas. 

    ![Azure Notification Hubs - Test d’envoi](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)


[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


### <a name="run-the-mobile-app"></a>Exécution de l'application mobile
Si vous souhaitez effectuer les tests de notifications Push sur un émulateur, vérifiez que votre image d’émulateur prend en charge le niveau d’API Google que vous avez choisi pour votre application. Si votre image ne prend pas en charge les API Google natives, l’exception **SERVICE\_NOT\_AVAILABLE** est levée.

De plus, assurez-vous que vous avez ajouté votre compte Google à l’émulateur en cours d’exécution sous **Paramètres** > **Comptes**. Sinon, vos tentatives d’inscription auprès de GCM peuvent entraîner la levée de l’exception **AUTHENTICATION\_FAILED**.

1. Exécutez l’application et notez qu’un ID d’inscription apparaît quand l’inscription réussit.
   
    ![Tests sur Android - Inscription de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Entrez le message de notification à envoyer à tous les appareils Android inscrits auprès du hub.
   
    ![Tests sur Android - envoi d’un message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Appuyez sur **Envoyer une notification**. Tous les appareils sur lesquels l’application est en cours d’exécution affichent une instance `AlertDialog` comportant le message de notification Push. Les appareils sur lesquels l’application n’est pas en cours d’exécution, mais qui ont déjà été inscrits aux notifications Push, reçoivent une notification dans le gestionnaire de notifications Android. Vous pouvez afficher ces notifications en effectuant un balayage vers le bas depuis l’angle supérieur gauche.
   
    ![Tests sur Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé Firebase Cloud Messaging pour envoyer des notifications Push à des appareils Android. Pour savoir comment envoyer des notifications Push à l’aide de Google Cloud Messaging, passez au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Notifications Push aux appareils Android à l’aide de Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)


<!-- Images. -->


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portail Azure]: https://portal.azure.com
