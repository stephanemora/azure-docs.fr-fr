---
title: Envoyer des notifications Push à Xamarin avec Azure Notification Hubs | Microsoft Docs
description: Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin iOS.
services: notification-hubs
keywords: notifications push iOS, messages push, notifications push, envoi de messages
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221134"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Tutoriel : Envoyer des notifications Push vers des applications Xamarin.iOS à l’aide d’Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Vous créez une application Xamarin.iOS vide qui reçoit des notifications Push à l’aide du [service de notification Push Apple (APN, Apple Push Notification)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l’exemple [Application NotificationHubs][GitHub].

Dans ce didacticiel, vous créez/mettez à jour le code pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Générer le fichier de demande de signature de certificat
> * Inscription de votre application pour les notifications Push
> * Création d’un profil d’approvisionnement pour l’application
> * Configuration de votre hub de notification pour les notifications Push iOS
> * Envoi de notifications Push de test

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Version la plus récente de [Xcode][Install Xcode]
* Un appareil compatible iOS 10 (ou version ultérieure)
* [programme pour développeurs Apple](https://developer.apple.com/programs/)
* [Visual Studio pour Mac]
  
  > [!NOTE]
  > En raison des exigences de configuration requise pour les notifications Push iOS, vous devez déployer et tester l’exemple d’application sur un appareil iOS physique (iPhone ou iPad) au lieu d’un simulateur.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels consacrés à Notification Hubs pour applications Xamarin. iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Connexion de votre application au hub de notification

### <a name="create-a-new-project"></a>Création d'un projet

1. Dans Visual Studio, créez un projet iOS, sélectionnez le modèle **Application avec affichage unique**, puis cliquez sur **Suivant**.

     ![Visual Studio : Sélectionner le type d’application][31]

2. Entrez le nom de l’application et l’identifiant de votre organisation, sélectionnez ensuite **Suivant**, puis **Créer**.

3. Dans la vue des solutions, double-cliquez sur *Info.plist*. Ensuite, sous **Identité**, assurez-vous que l’identificateur de l’offre groupée correspond à celui utilisé lors de la création du profil d’approvisionnement. Sous **Signature**, vérifiez que votre compte de développeur est sélectionné sous **Team**, que l’option Automatically manage signing (Gérer automatiquement la signature) est activée et que votre certificat de signature et votre profil d’approvisionnement sont automatiquement sélectionnés.

    ![Visual Studio : Configuration des applications iOS][32]

4. Dans la vue des solutions, double-cliquez sur `Entitlements.plist` et vérifiez que l’option **Activer les notifications Push** est cochée.

    ![Visual Studio : configuration des droits iOS][33]

5. Ajoutez le package de messagerie Azure. Dans la vue des solutions, cliquez sur le projet avec le bouton droit de la souris, puis sélectionnez **Ajouter** > **Ajouter des paquets NuGet**. Recherchez le package **Xamarin.Azure.NotificationHubs.iOS** et ajoutez-le à votre projet.

6. Ajoutez un nouveau fichier à votre classe et nommez-le `Constants.cs`. Ensuite, ajoutez les variables suivantes et remplacez les espaces réservés des littéraux de chaînes par l’élément `hubname` et l’élément `DefaultListenSharedAccessSignature` notés précédemment.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. Dans `AppDelegate.cs`, ajoutez les instructions using suivantes :

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. Créez une implémentation de `MSNotificationHubDelegate` dans `AppDelegate.cs` :

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. Dans `AppDelegate.cs`, mettez à jour `FinishedLaunching()` afin qu’il corresponde au code suivant :

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. Dans `AppDelegate.cs`, implémentez la méthode `DidReceivePushNotification` pour la classe `AzureNotificationHubListener` :

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. Exécutez l'application sur votre appareil.

## <a name="send-test-push-notifications"></a>Envoi de notifications Push de test

Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* du [Azure portal]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send][30]

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Apps ou ASP.NET à l’aide d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre backend, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous envoyez des notifications de diffusion à tous vos appareils iOS inscrits avec le serveur principal. Pour savoir comment envoyer des notifications à des appareils iOS spécifiques, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio pour Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure portal]: https://portal.azure.com
