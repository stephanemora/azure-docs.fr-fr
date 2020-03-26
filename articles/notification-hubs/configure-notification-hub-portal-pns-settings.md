---
title: Configurer les notifications Push dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer Azure Notification Hubs sur le portail Azure à l’aide de paramètres PNS (Platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406969"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Configurer les notifications Push dans un hub de notification sur le portail Azure

Azure Notification Hubs propose un moteur Push facile à utiliser et à mettre à l’échelle horizontalement (« scale-out »). Utilisez Notification Hubs pour envoyer des notifications à n’importe quelle plateforme (iOS, Android, Windows, Baidu) et à partir de n’importe quel back-end (cloud ou local). Pour plus d’informations, consultez [Présentation d’Azure Notification Hubs](notification-hubs-push-notification-overview.md).

Dans ce guide de démarrage rapide, vous allez utiliser les paramètres PNS (Platform Notification System) de Notification Hubs pour configurer des notifications Push sur plusieurs plateformes. Le guide de démarrage rapide vous montre les étapes à suivre sur le portail Azure.

Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Pour configurer Apple Push Notification Service (APNS) :

1. Dans le portail Azure, dans la page **Hub de notification**, sélectionnez **Apple (APNS)** dans le menu de gauche.

1. Pour **Mode d’authentification**, sélectionnez **Certificat** ou **Jeton**.

   a. Si vous sélectionnez **Certificat** :
   * Sélectionnez l’icône de fichier, puis sélectionnez le fichier *.p12* que vous voulez charger.
   * Entrez un mot de passe.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un certificat APNS sur le portail Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Si vous sélectionnez **Jeton** :

   * Entrez les valeurs pour **ID de clé**, **ID d’offre groupée**, **ID de l’équipe** et **Jeton**.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un jeton APNS sur le portail Azure](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Pour plus d’informations, consultez [Notifications Push vers iOS avec Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

Pour configurer des notifications Push pour Google Firebase Cloud Messaging (FCM) :

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Google (GCM/FCM)** dans le menu de gauche. 
2. Collez la **Clé API** pour le projet FCM que vous avez enregistré précédemment. 
3. Sélectionnez **Enregistrer**. 

   ![Capture d’écran montrant comment configurer Notification Hubs pour Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Une fois ces étapes terminées, une alerte indique que le hub de notification a été correctement mis à jour. Le bouton **Enregistrer** est désactivé. 

Pour plus d’informations, consultez [Notifications Push vers des appareils Android avec Notification Hubs et Google FM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Services de notifications Push Windows

Pour configurer les services de notifications Push Windows (WNS) :

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Windows (WNS)** dans le menu de gauche.
2. Entrez des valeurs pour **SID de package** et **Clé de sécurité**.
3. Sélectionnez **Enregistrer**.

   ![Capture d’écran montrant les zones SID du Package et Clé de sécurité](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Pour plus d’informations, consultez [Envoyer des notifications vers des applications UWP avec Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Services de notifications Push Microsoft pour Windows Phone

Pour configurer les services de notifications Push Microsoft (MPNS) pour Windows Phone : 

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Windows Phone (MPNS)** dans le menu de gauche.
1. Activez les notifications Push authentifiées ou non authentifiées :

   a. Pour activer les notifications Push non authentifiées, sélectionnez **Activer les notifications Push non authentifiées** > **Enregistrer**.

      ![Capture d’écran montrant comment activer les notifications Push non authentifiées](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Pour activer les notifications Push authentifiées :
      * Dans la barre d’outils, sélectionnez **Charger le certificat**.
      * Sélectionnez l’icône de fichier, puis le fichier de certificat.
      * Entrez le mot de passe du certificat.
      * Sélectionnez **OK**.
      * Dans la page **Windows Phone (MPNS)** , sélectionnez **Enregistrer**.

Pour plus d’informations, consultez [Notifications Push vers des applications Windows Phone avec Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Pour configurer des notifications Push pour Baidu :

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Baidu (Android China)** dans le menu de gauche. 
2. Entrez la **Clé API** que vous avez obtenue à partir de la console Baidu du projet Push cloud Baidu. 
3. Entrez la **Clé secrète** que vous avez obtenue à partir de la console Baidu du projet Push cloud Baidu. 
4. Sélectionnez **Enregistrer**. 

    ![Capture d’écran de Notification Hubs montrant la configuration de Baidu (Android China) pour les notifications Push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Une fois ces étapes terminées, une alerte indique que le hub de notification a été correctement mis à jour. Le bouton **Enregistrer** est désactivé. 

Pour plus d’informations, consultez [Prendre en main Notification Hubs à l’aide de Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris à configurer les paramètres PNS (Platform Notification System) pour un hub de notification sur le portail Azure. 

Pour en savoir plus sur l’envoi de notifications Push vers différentes plateformes, consultez ces tutoriels :

- [Notifications Push vers des appareils iOS avec Notification Hubs et APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Notifications Push vers des appareils Android avec Notification Hubs et Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Notifications Push vers une application UWP sur un appareil Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Notifications Push vers une application Windows Phone 8 avec MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Notifications Push avec Notification Hubs et le push cloud Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
