---
title: Envoyer des notifications Push vers iOS avec Azure Notification Hubs et le SDK iOS version 3.0.0 préversion 1
description: Dans ce tutoriel, vous allez découvrir comment utiliser Azure Notification Hubs et Apple Push Notification Service pour envoyer des notifications Push à des appareils iOS (version 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100597398"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Tutoriel : Envoyer des notifications Push vers des applications iOS avec le SDK Azure Notification Hubs pour Apple

Ce tutoriel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS avec le SDK Azure Notification Hubs pour Apple.

Ce didacticiel couvre les étapes suivantes :

- Créer un exemple d’application iOS
- Connecter votre application iOS à Azure Notification Hubs
- Envoyer des notifications Push de test
- Vérifier la réception des notifications par votre application

Vous pouvez télécharger le code complet pour ce tutoriel à partir de [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des prérequis suivants :

- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Un iPhone ou un iPad exécutant iOS version 10 ou ultérieure
- Votre appareil physique inscrit auprès du [portail Apple](https://developer.apple.com/) et associé à votre certificat

Avant de continuer, veillez à suivre le tutoriel précédent sur la prise en main d’[Azure Notification Hubs pour les applications iOS](ios-sdk-get-started.md) afin de configurer les informations d’identification Push dans votre hub de notification. Même si vous n’avez aucune expérience préalable avec le développement sur iOS, vous devriez être en mesure d’effectuer ces étapes.

> [!NOTE]
> En raison des conditions de configuration requises pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil iOS physique (iPhone ou iPad) au lieu de l’émulateur iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connexion de votre application iOS à Notification Hubs

1. Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View Application** .

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Sélectionner un modèle":::

2. Au moment de définir les options de votre nouveau projet, veillez à utiliser le même **nom de produit** et le même **identificateur d’organisation** que ceux utilisés lorsque vous avez défini l’identifiant d’offre groupée dans le portail des développeurs Apple.

3. Sous le navigateur de projet, sélectionnez le nom de votre projet sous **Targets**, puis sélectionnez l’onglet **Signing & Capabilities**. Vérifiez que vous sélectionnez l’**équipe** correspondant à votre compte de développeur Apple. XCode doit extraire automatiquement le profil d’approvisionnement que vous avez créé précédemment en fonction de l’identificateur d’offre groupée.

   Si vous ne voyez pas le nouveau profil d’approvisionnement que vous avez créé dans Xcode, essayez d’actualiser les profils pour votre identité de signature. Cliquez sur **Xcode** dans la barre de menus, sur **Preferences**, sur l’onglet **Account**, sur le bouton **View Details**, sur votre identité de signature, puis cliquez sur le bouton d’actualisation dans le coin inférieur droit.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Afficher les détails":::

4. Sous l’onglet **Signing & Capabilities**, sélectionnez **+ Capability**. Double-cliquez sur **Push Notifications** pour l’activer.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Fonctionnalité":::

5. Ajoutez les modules du Kit de développement logiciel (SDK) Azure Notification Hubs.

   Vous pouvez intégrer le kit SDK Azure Notification Hubs à votre application en utilisant [Cocoapods](https://cocoapods.org/) ou en ajoutant manuellement les fichiers binaires à votre projet.

   - Intégration par le biais de Cocoapods : Ajoutez les dépendances suivantes à votre podfile pour inclure le SDK Azure Notification Hubs dans votre application :

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Exécutez pod install pour installer votre pod récemment défini et ouvrir votre .xcworkspace.

         Si le message d’erreur **Unable to find a specification for AzureNotificationHubs-iOS** s’affiche lors de l’exécution de pod install, exécutez `pod repo update` pour obtenir les derniers pods à partir du référentiel Cocoapods, puis exécutez pod install.

   - Intégration par le biais de Carthage : Ajoutez les dépendances suivantes à votre Cartfile pour inclure le SDK Azure Notification Hubs dans votre application :

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Ensuite, mettez à jour les dépendances de build :

      ```shell
      $ carthage update
      ```

      Pour plus d’informations sur l’utilisation de Carthage, voir le [référentiel GitHub Carthage](https://github.com/Carthage/Carthage).

   - Intégration en copiant les binaires dans votre projet :

      Vous pouvez intégrer en copiant les binaires dans votre projet comme suit :

        - Téléchargez le fichier zip du framework [Kit SDK Azure Notification Hubs](https://github.com/Azure/azure-notificationhubs-iOS/releases/) et décompressez-le.

        - Dans Xcode, cliquez avec le bouton droit sur votre projet et sélectionnez l’option **Add Files to** pour ajouter le dossier **WindowsAzureMessaging.framework** à votre projet Xcode. Sélectionnez **Options**, assurez-vous que l’option **Copy items if needed** (Copier les éléments si nécessaire) est cochée, puis cliquez sur **Add** (Ajouter).

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Ajouter le framework":::

6. Ajoutez ou modifiez un fichier appelé **DevSettings.plist** qui contient deux propriétés, `CONNECTION_STRING` pour la chaîne de connexion au hub de notification Azure et `HUB_NAME` pour le nom du hub de notification Azure.

7. Ajoutez les informations pour la connexion à Azure Notification Hubs dans la section `<string></string>` appropriée.  Remplacez les espaces réservés de littéral de chaîne `--HUB-NAME--` et `--CONNECTION-STRING--` par le nom du hub et **DefaultListenSharedAccessSignature**, respectivement, que vous avez précédemment obtenus dans le portail :

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. Dans le même fichier **AppDelegate.m**, remplacez tout le code situé après `didFinishLaunchingWithOptions` par le code suivant :

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Ce code se connecte au hub de notification en utilisant les informations de connexion que vous avez spécifiées dans **DevSettings.plist**. Il transmet ensuite le jeton de l’appareil au hub de notification de sorte que ce dernier puisse envoyer des notifications.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Créer le fichier d’en-tête NotificationDetailViewController

1. À l’instar des instructions précédentes, ajoutez un autre fichier d’en-tête nommé **SetupViewController.h**. Remplacez le contenu du nouveau fichier d’en-tête par le code suivant :

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Ajoutez le fichier d’implémentation **SetupViewController.m**. Remplacez le contenu du fichier par le code suivant, qui implémente les méthodes UIViewController :

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Pour vérifier l’absence d’échecs, générez et exécutez l’application sur votre appareil.

## <a name="send-test-push-notifications"></a>Envoi de notifications Push de test

Vous pouvez tester la réception de notifications dans votre application avec l’option **Test Send** du [Azure portal](https://portal.azure.com/). Cette option envoie une notification Push de test à votre appareil.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Envoi de test":::

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Apps ou ASP.NET à l’aide d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre backend, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

La liste ci-dessous répertorie certains autres didacticiels, que vous pouvez consulter pour envoyer des notifications :

- Azure Mobile Apps : pour découvrir un exemple de la procédure d’envoi de notifications à partir d’un serveur principal Mobile Apps intégré à Notification Hubs, consultez l’article [Ajout de notifications Push à votre application iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET : [Utiliser Notification Hubs pour envoyer des notifications Push à des utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Kit de développement logiciel (SDK) Java pour Azure Notification Hubs : consultez [Utilisation de Notification Hubs à partir de Java](notification-hubs-java-push-notification-tutorial.md) pour envoyer des notifications depuis Java. Il a été testé dans Eclipse pour le développement Android.
- PHP : [Utilisation de Notification Hubs à partir de PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Vérification de la réception des notifications Push par votre application

Pour tester les notifications Push sur iOS, vous devez déployer l’application sur un appareil iOS physique. Vous ne pouvez pas envoyer de notifications Push Apple en utilisant le simulateur iOS.

1. Exécutez l’application, vérifiez que l’inscription est effectuée, puis appuyez sur **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="S’inscrire":::

2. Ensuite, envoyez une notification Push de test à partir du [portail Azure](https://portal.azure.com/), comme indiqué dans la section précédente.

3. La notification Push est envoyée à tous les appareils qui sont inscrits pour recevoir les notifications à partir du hub de notification donné.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Envoyer un test":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications Push à tous vos appareils iOS inscrits. Pour découvrir comment envoyer des notifications à des appareils iOS spécifiques, passez au tutoriel suivant :

[Tutoriel : Notifications Push vers des appareils spécifiques](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble d’Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [API REST de Notification Hubs](/rest/api/notificationhubs/)
- [Kit de développement logiciel (SDK) Notification Hubs pour opérations backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Kit de développement logiciel (SDK) Notification Hubs sur GitHub](https://github.com/Azure/azure-notificationhubs)
- [S’inscrire auprès du principal d’application](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md)
- [Utilisation de balises](notification-hubs-tags-segment-push-message.md)
- [Utilisation des modèles personnalisés](notification-hubs-templates-cross-platform-push-messages.md)
- [Contrôle d’accès Service Bus avec des signatures d’accès partagé](../service-bus-messaging/service-bus-sas.md)
- [Générer des jetons SAS par programme](/rest/api/eventhub/generate-sas-token)
- [Sécurité Apple : CommonCrypto](https://developer.apple.com/security/)
- [Heure d’époque Unix](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)