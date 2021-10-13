---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838480"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>Conditions préalables supplémentaires pour les notifications push

Un compte Firebase sur lequel Cloud Messaging (FCM) est activé et avec votre service Firebase Cloud Messaging connecté à une instance Azure Notification Hub. Pour plus d’informations, consultez [Notifications Communication Services](../../../../concepts/notifications.md).
De plus, ce tutoriel suppose que vous utilisez Android Studio version 3.6 ou supérieure pour générer votre application.

Un jeu d’autorisations pour l’application Android est requis pour pouvoir recevoir des messages de notification de la part de Firebase Cloud Messaging. Dans votre fichier `AndroidManifest.xml`, ajoutez le jeu d’autorisations suivant juste après `<manifest ...>` ou sous la balise `</application>`.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>Vue d’ensemble
Les notifications Push mobiles sont les notifications contextuelles que vous recevez sur des appareils mobiles. Pour l’appel, nous utilisons des notifications Push VoIP (Voice over Internet Protocol). Nous effectuons l’inscription aux notifications push, nous traitons les notifications push, puis annulons l’inscription aux notifications push.

## <a name="register-for-push-notifications"></a>Inscription aux notifications Push

Pour s’inscrire aux notifications Push, l’application doit appeler `registerPushNotification()` sur une instance `CallAgent` avec un jeton d’inscription d’appareil.

Pour obtenir le jeton d’inscription de l’appareil, ajoutez le SDK Firebase au fichier `build.gradle` de votre module d’application en ajoutant les lignes suivantes à la section `dependencies` si elles n’y figurent pas encore :

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Dans le fichier *build.gradle* de votre niveau de projet, ajoutez l’élément suivant dans la section `dependencies` s’il n’y figure pas encore.

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

Ajoutez le plug-in suivant au début du fichier s’il n’y figure pas encore :

```groovy
apply plugin: 'com.google.gms.google-services'
```

Sélectionnez *Synchroniser maintenant* dans la barre d’outils. Ajoutez l’extrait de code suivant pour obtenir le jeton d’inscription de l’appareil généré par le SDK Firebase Cloud Messaging pour l’instance de l’application cliente. Veillez à ajouter les importations ci-dessous à l’en-tête de l’activité principale de l’instance. Celles-ci sont nécessaires pour que l’extrait de code récupère le jeton :

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Ajoutez cet extrait de code pour récupérer le jeton :

```java
FirebaseInstanceId.getInstance().getInstanceId()
    .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
        @Override
        public void onComplete(@NonNull Task<InstanceIdResult> task) {
            if (!task.isSuccessful()) {
                Log.w("PushNotification", "getInstanceId failed", task.getException());
                return;
            }

            // Get new Instance ID token
            String deviceToken = task.getResult().getToken();
            // Log
            Log.d("PushNotification", "Device Registration token retrieved successfully");
        }
    });
```
Inscrivez le jeton d’inscription d’appareil auprès du SDK Calling Services pour les notifications Push des appels entrants :

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

## <a name="push-notification-handling"></a>Gestion des notifications Push

Pour recevoir les notifications Push d’appel entrant, appelez *handlePushNotification()* sur une instance *CallAgent* avec une charge utile.

Pour obtenir la charge utile depuis Firebase Cloud Messaging, commencez par créer un nouveau Service (Fichier > Nouveau > Service > Service) qui étend la classe du SDK Firebase *FirebaseMessagingService* et remplacez la méthode `onMessageReceived`. Cette méthode est le gestionnaire d’événements appelé lorsque Firebase Cloud Messaging remet la notification Push à l’application.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Ajoutez la définition de service suivante au fichier `AndroidManifest.xml`, dans la balise `<application>` :

```xml
<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- Une fois la charge utile récupérée, elle peut être transmise au SDK *Communication Services* afin d’être analysée en un objet *IncomingCallInformation* qui sera géré en appelant la méthode *handlePushNotification* sur une instance de *CallAgent*. Une instance `CallAgent` est créée en appelant la méthode `createCallAgent(...)` sur la classe `CallClient`.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Lorsque la gestion du message de notification Push est réussie et que tous les gestionnaires d’événements sont correctement inscrits, l’application sonne.

## <a name="unregister-push-notifications"></a>Désinscription des notifications Push

Les applications peuvent annuler l’inscription aux notifications Push à tout moment. Appelez la méthode `unregisterPushNotification()` sur callAgent pour procéder à la désinscription.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```