---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838482"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>Configurer des notifications Push

Une notification Push mobile est la notification contextuelle que vous recevez sur un appareil mobile. Pour l’appel, nous utilisons des notifications Push VoIP (protocole voix sur IP). 

Les sections suivantes expliquent comment s’inscrire à des notifications Push, les gérer et annuler cette inscription. Avant de démarrer ces tâches, effectuez les prérequis suivants :

1. Dans Xcode, accédez à **Signing & Capabilities** (Signature et fonctionnalités). Ajoutez une fonctionnalité en sélectionnant **+ Capability** (Fonctionnalité), puis **Push Notifications** (Notifications Push).
2. Ajoutez une autre fonctionnalité en sélectionnant **+ Capability**, puis **Background Modes** (Modes d’arrière-plan).
3. Sous **Background Modes**, cochez les cases **Voice over IP** (Voix sur IP) et **Remote notifications** (Notifications distantes).

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="Capture d’écran qui montre comment ajouter des fonctionnalités dans Xcode." lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Inscription aux notifications Push

Pour vous inscrire aux notifications Push, appelez `registerPushNotification()` sur une instance `CallAgent` avec un jeton d’inscription d’appareil.

L’inscription aux notifications Push doit avoir lieu une fois l’initialisation correctement effectuée. Quand l’objet `callAgent` est détruit, `logout` est appelé, ce qui annule automatiquement l’inscription aux notifications push.

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>Gérer les notifications Push
Pour recevoir les notifications Push relatives aux appels entrants, appelez `handlePushNotification()` sur une instance `CallAgent` avec une charge utile de dictionnaire.

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>Désinscription des notifications Push

Les applications peuvent annuler l’inscription aux notifications Push à tout moment. Appelez simplement la méthode `unregisterPushNotification` sur `CallAgent`.

> [!NOTE]
> Les applications ne sont pas automatiquement désinscrites des notifications Push lors de la déconnexion.

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```