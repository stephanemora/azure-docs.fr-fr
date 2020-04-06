---
title: Envoyer des notifications Push vers des applications iOS Swift utilisant Azure Notification Hubs | Microsoft Docs
description: Apprenez comment envoyer des notifications push vers des applications iOS Swift qui utilisent Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336634"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Tutoriel : Envoyer des notifications Push vers des applications iOS Swift utilisant l’API REST Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Dans ce didacticiel, vous utilisez Azure Notification Hubs pour envoyer des notifications Push à une application iOS Swift à l’aide de l’[API REST](/rest/api/notificationhubs/). Vous créez aussi une application iOS vide qui reçoit des notifications Push à l’aide du [service de notification Push Apple (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Ce didacticiel vous familiarise avec les procédures suivantes :

> [!div class="checklist"]
> * Génération du fichier de demande de signature de certificat
> * Inscription de votre application pour les notifications Push
> * Création d’un profil de mise en service pour l’application
> * Créer un hub de notification.
> * Configuration du hub de notification avec des informations APNs
> * Connexion de votre application iOS à un hub de notification
> * Test de la solution.

Le code complet de ce didacticiel est disponible sur [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Prérequis

Pour suivre l’article, vous devez préalablement :

- Pour suivre la [vue d’ensemble d’Azure Notification Hubs](notification-hubs-push-notification-overview.md), si vous n’êtes pas familiarisé avec ce service.
- Pour en savoir plus sur les [inscriptions et l’installation](notification-hubs-push-notification-registration-management.md).
- Un [compte de développeur Apple](https://developer.apple.com) actif.
- Un Mac exécutant Xcode, ainsi qu’un certificat de développeur valide installé dans votre trousseau.
- Un iPhone que vous pouvez utiliser et avec lequel vous pouvez déboguer, car vous ne pouvez pas tester les notifications push avec le simulateur.
- Votre iPhone inscrit dans le [portail Apple](https://developer.apple.com) et associé à votre certificat.
- Un [abonnement Azure](https://portal.azure.com) où vous pouvez créer et gérer les ressources.

Même si vous n’avez aucune expérience préalable avec le développement sur iOS, vous devriez être en mesure de suivre les étapes de création de cet exemple de principes de base. Toutefois, si vous connaissez, vous connaîtrez les concepts suivants :

- Création d’applications iOS avec Xcode et Swift.
- Configuration d’un [Azure Notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) pour iOS.
- Le [portail des développeurs Apple](https://developer.apple.com) et le [portail Azure](https://portal.azure.com).

> [!NOTE]
> Le hub de notification sera configuré pour utiliser le mode d’authentification **bac à sable** uniquement. Vous ne devez pas utiliser ce mode d’authentification pour les charges de travail de production.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Connexion de votre application iOS à un hub de notification

Dans cette section, vous allez créer l’application iOS qui se connecte au hub de notification.  

### <a name="create-an-ios-project"></a>Création d’un projet iOS

1. Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View Application** .

1. Lorsque vous définissez les options pour le nouveau projet :

   1. Spécifiez le **Nom du produit** (PushDemo) et l’**identificateur de l’organisation** (`com.<organization>`) que vous avez utilisé lorsque vous avez défini l’**identificateur de l’offre groupée** dans le portail des développeurs Apple.

   1. Choisissez l’**Équipe** pour laquelle l’**ID de l’application** a été configuré.

   1. Définissez le **langage** sur **Swift**.

   1. Sélectionnez **Suivant**.

1. Créez un dossier appelé **SupportingFiles**.

1. Créez un fichier p-list nommé **devsettings.plist** dans le dossier **SupportingFiles**. Veillez à ajouter ce dossier à votre fichier **gitignore** afin qu’il ne soit pas validé lorsque vous travaillez avec un référentiel git. Dans une application de production, vous allez probablement configurer ces secrets de façon conditionnelle dans le cadre d’un processus de création automatisé. Ces paramètres ne sont pas traités dans cette guide pas à pas.

1. Mettez à jour **devsettings.plist** pour inclure les entrées de configuration suivantes à l’aide de vos propres valeurs à partir du hub de notification que vous avez configuré :

   | Clé                            | Type                     | Valeur                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Vous pouvez trouver les valeurs requises en accédant aux ressources du hub de notification dans le portail Azure. Plus particulièrement, les valeurs **notificationHubName** et **notificationHubNamespace** se trouvent dans le coin supérieur droit du résumé **Essentials** dans la page **Vue d’ensemble**.

   ![Résumé Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Vous pouvez également trouver les valeurs **notificationHubKeyName** et **notificationHubKey** en accédant à **Stratégies d’accès** et en sélectionnant la  **Stratégie d’accès** adéquate, telle que `DefaultFullSharedAccessSignature`. Après cela, copiez dans la **Chaîne de connexion principale** la valeur préfixée avec `SharedAccessKeyName=` pour `notificationHubKeyName` et la valeur préfixée avec `SharedAccessKey=` pour `notificationHubKey`.

   La chaîne de connexion doit être dans ce format :

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Pour simplifier, spécifiez `DefaultFullSharedAccessSignature` pour pouvoir utiliser le jeton pour envoyer des notifications. En pratique, la valeur `DefaultListenSharedAccessSignature` constitue un meilleur choix pour des situations où vous ne souhaitez recevoir que des notifications.

1. Sous **Navigateur de projets**, sélectionnez **Nom du projet** puis l’onglet **Général**.

1. Trouvez l’**Identité** puis définissez la valeur **Identificateur de l’offre groupée** pour la faire correspondre à `com.<organization>.PushDemo`, qui est la valeur utilisée pour l’**ID de l’application** d’une étape précédente.

1. Trouvez **Signature et fonctionnalités**, puis sélectionnez l’**Équipe** correspondant à votre **Compte de développeur Apple**. La valeur **Équipe** doit correspondre à celle sous laquelle vous avez créé vos certificats et vos profils.

1. XCode doit extraire automatiquement la valeur du **Profil d’approvisionnement** correspondant en fonction de l’**Identificateur d’offre groupée**. Si vous ne voyez pas la nouvelle valeur **Profil d’approvisionnement**, essayez d’actualiser les profils de l’**Identité de signature** en sélectionnant **Xcode** > **Préférences** > **Compte**, puis sélectionnez le bouton **Télécharger des profils manuels** pour télécharger les profils.

1. Toujours sous l’onglet **Signature et fonctionnalités**, cliquez sur le bouton **+ Fonctionnalité** et appuyez deux fois sur **Notifications Push** dans la liste pour vous assurer que les **notifications Push** sont activées.

1. Ouvrez votre fichier **AppDelegate.swift** pour implémenter le protocole **UNUserNotificationCenterDelegate** et ajoutez le code suivant en haut de la classe :

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Vous utiliserez les membres plus tard. Plus précisément, vous allez utiliser le membre **tags** dans le cadre de l'inscription à l'aide d'un **modèle personnalisé**. Pour plus d’informations sur les balises, consultez [Balises pour les inscriptions](notification-hubs-tags-segment-push-message.md) et [Inscriptions de modèles](notification-hubs-templates-cross-platform-push-messages.md).

1. Dans le même fichier, ajoutez le code suivant à la fonction **didFinishLaunchingWithOptions** :

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Ce code récupère les valeurs de configuration du fichier **devsettings.plist**, définit la classe **AppDelegate** comme délégué **UNUserNotificationCenter**, demande l’autorisation d’envoyer des notifications push, puis appelle **registerForRemoteNotifications**.

    Pour simplifier, le code prend en charge la version *iOS 10 et supérieures uniquement*. Vous pouvez ajouter la prise en charge pour les versions de systèmes d’exploitation antérieures en utilisant de façon conditionnelle les API et approches respectives, comme vous le feriez normalement.

1. Dans ce même fichier, ajoutez les fonctions suivantes :

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Le code utilise les valeurs **installationId** et **pushChannel** pour s’inscrire avec le hub de notification. Dans ce cas, vous utilisez **UIDevice.current.identifierForVendor** pour fournir une valeur unique et identifier l’appareil, puis vous formatez le **deviceToken** pour fournir la valeur **pushChannel** souhaitée. La fonction **showAlert** existe pour afficher quelques messages textuels à des fins de démonstration.

1. Toujours dans le fichier **AppDelegate.swift**, ajoutez les fonctions **willPresent** et **didReceive** à **UNUserNotificationCenterDelegate**. Ces fonctions afficheront une alerte lorsqu’elles sont informées qu’une application s’exécute en premier plan ou en arrière-plan.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Ajoutez des instructions d’impression en bas de la fonction **didRegisterForRemoteNotificationsWithDeviceToken** pour vérifier que **installationId** et **pushChannel** disposent de valeurs.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Créez les dossiers **Modèles**, **Services** et **Utilitaires** pour les composants fondamentaux que vous ajouterez au projet plus tard.

1. Vérifiez que le projet se crée et s’exécute sur un appareil physique. Les notifications push ne peuvent pas être testées à l’aide du simulateur.

### <a name="create-models"></a>Créer des modèles

Dans cette étape, vous allez créer un ensemble de modèles pour représenter les charges utiles de l’[API REST Notification Hubs](/rest/api/notificationhubs/) et pour stocker les données de jeton de signature d’accès partagé (SAS).

1. Ajoutez un nouveau fichier Swift nommé **PushTemplate.swift** dans le dossier **Modèles**. Ce modèle fournit une structure représentant le **CORPS** d’un modèle individuel dans la charge utile **DeviceInstallation**.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Ajoutez un nouveau fichier Swift nommé **DeviceInstallation.swift** dans le dossier **Modèles**. Ce fichier définit une structure qui représente la charge utile de création et de mise à jour d’une **Installation d’appareil**. Ajoutez le code suivant au fichier :

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Ajoutez un nouveau fichier Swift nommé **TokenData.swift** dans le dossier **Modèles**. Ce modèle sera utilisé pour stocker un jeton SAS, ainsi que son expiration.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Générer un jeton SAS

Les hubs de notification utilisent la même infrastructure de sécurité qu’Azure Service Bus. Pour appeler l’API REST, vous devrez [générer par programmation un jeton SAS](/rest/api/eventhub/generate-sas-token) qui peut être utilisé dans l’en-tête d’**Autorisation** de la requête.  

Le jeton obtenu présente le format suivant :

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Le processus implique les six mêmes étapes clé :  

1. Calculer l’expiration au format [heure (époque) UNIX](https://en.wikipedia.org/wiki/Unix_time), ce qui signifie le nombre de secondes écoulées depuis le temps universel coordonné, le 1er janvier 1970 à minuit.
1. Mettre en forme **ResourceURL ne** qui représente la ressource à laquelle vous tentez d’accéder pour qu’elle soit encodée en pourcentage et en minuscules. **ResourceURL** se présente sous la forme `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Préparer **StringToSign**, qui est mis en forme en tant que `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Calculer et encoder en Base64 la **Signature** à l’aide du hachage HMAC-SHA256 de la valeur **StringToSign**. La valeur de hachage est utilisée avec le **clé** de la **chaîne de connexion** pour la **règle d’autorisation** correspondante.
1. Mettre en forme la **Signature** encodée en Base64 pour qu’elle soit encodée en pourcentage.
1. Construire le jeton au format attendu à l’aide des valeurs **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, et **UrlEncodedResourceUrl**.

Consultez la [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md) pour obtenir plus d’informations sur la signature d’accès partagé et comment Azure Service Bus et Notification Hubs l’utilisent.

Dans le cadre de cet exemple Swift, vous allez utiliser la bibliothèque open source d’Apple **CommonCrypto** pour faciliter le hachage de la signature. Étant donné qu’il s’agit d’une bibliothèque C, elle n’est pas immédiatement accessible en Swift. Vous pouvez rendre disponible la bibliothèque à l’aide d’un en-tête de pontage.

Pour ajouter et configurer l’en-tête de pontage :

1. Dans Xcode, sélectionnez **File** > **New** > **File** > **Header File**. Nommez le fichier d’en-tête **BridgingHeader.h**.

1. Modifiez le fichier à importer **CommonHMAC.h** :

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Mettez à jour les **Paramètres de Build** de la cible pour référencer l’en-tête de pontage :

   1. Appuyez sur le projet **PushDemo**, puis faites défiler jusqu’à la section **Swift Compiler**.

   1. Vérifiez que l’option **Install Objective-C Compatibility Header** est définie sur **Yes**.

   1. Entrez le chemin `'<ProjectName>/BridgingHeader.h'` dans l’option **Objective-C bridging Header**. Il s’agit du chemin d’accès vers notre en-tête de pontage.

   Si vous ne trouvez pas ces options, assurez-vous de sélectionner la vue **Tout** au lieu de **De base** ou **Personnalisé**.

   Il existe de nombreuses bibliothèques wrapper tierces open source disponibles pouvant simplifier l’utilisation de **CommonCrypto**. Toutefois, nous ne parlerons de ces bibliothèques dans le cadre de cet article.

1. Ajoutez un nouveau fichier Swift nommé **TokenUtility.swift** dans le dossier **Utilitaires** et ajoutez le code suivant :

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Cet utilitaire encapsule la logique chargée de générer le jeton SAS.

   Comme indiqué précédemment, la fonction **getSasToken** orchestre les étapes requises pour préparer le jeton. La fonction sera appelée par le service d’installation plus loin dans ce didacticiel.

   Les deux autres fonctions sont appelées par la fonction **getSasToken** : **sha256HMac** pour calculer la signature et **urlEncodedString** pour encoder la chaîne URL associée. La fonction **urlEncodedString** est nécessaire car il n’est pas possible d’atteindre la sortie requise en utilisant la fonction intégrée **addingPercentEncoding**.

   Le [kit de développement logiciel (SDK) Stockage Azure iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) est un excellent exemple de comment aborder ces opérations dans Objective-C. Vous trouverez plus d’informations sur les jetons SAS Azure Service Bus dans la [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

1. Dans **AppDelegate.swift**, ajoutez le code suivant à la fonction *didRegisterForRemoteNotificationsWithDeviceToken* pour vérifier que **TokenUtility.getSasToken** génère un jeton valide
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Veillez à remplacer les valeurs d’espace réservé de la chaîne **baseAddress** par vos propres valeurs.

### <a name="verify-the-sas-token"></a>Vérifier le jeton SAS

Avant d’implémenter le service d’installation dans le client, vérifiez que notre application génère correctement le jeton SAS à l’aide de l’utilitaire HTTP de votre choix. Dans le cadre de ce didacticiel, nous avons choisi d’utiliser **Postman**.

Notez les valeurs **installationId** et **token** générées par l'application.

Suivez ces étapes pour appeler l’API des **installations** :

1. Dans **Postman**, ouvrez un nouvel onglet.

1. Définissez la requête **GET** et spécifiez l’adresse suivante :

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configurez les en-têtes de requête comme suit :

   | Clé           | Valeur            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorisation | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Sélectionnez le bouton **Code** qui apparaît dans le coin supérieur droit sous le bouton **Enregistrer**. La requête doit ressembler à l’exemple suivant :

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Sélectionnez le bouton **Envoyer**.

Aucun enregistrement n’existe pour la valeur spécifiée **installationId** à ce stade. La vérification doit aboutir à une réponse « 404 Introuvable » plutôt qu’à une réponse « 401 non autorisé ». Ce résultat doit confirmer que le jeton SAS a été accepté.

### <a name="implement-the-installation-service-class"></a>Implémenter la classe de service d’installation

Ensuite, vous allez implémenter notre wrapper de base autour de l’API REST [Installations](/rest/api/notificationhubs/create-overwrite-installation).  

Ajoutez un nouveau fichier Swift nommé **NotificationRegistrationService.swift** dans le dossier **Services**, puis ajoutez le code suivant à ce fichier :

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Les détails requis sont fournis dans le cadre de l’initialisation. Les balises et les modèles sont éventuellement passés dans la fonction **register** pour former une partie de la charge utile JSON **Installation de périphérique**.  

La fonction **register** appelle les autres fonctions privées pour préparer la demande. Une fois une réponse reçue, l’achèvement est appelé et indique si l’inscription a réussi.  

Le point de terminaison de la requête est créé par la fonction **getBaseAddress**. La construction utilise les paramètres du hub de notification *namespace* et *name* fournis lors de l’initialisation.  

La fonction **getSasToken** vérifie si le jeton actuellement stocké est valide. Si le jeton n’est pas valide, la fonction appelle **TokenUtility** pour générer un nouveau jeton, puis le stocke avant de retourner une valeur.

Enfin, **encodeToJson** convertit les objets de modèle en question en JSON pour une utilisation en tant que partie intégrante du corps de la requête.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invoquer l’API REST de Notification Hubs

La dernière étape est la mise à jour de **AppDelegate** pour utiliser **NotificationRegistrationService** pour s’inscrire auprès de notre **NotificationHub**.

1. Ouvrez **AppDelegate.swift** et ajoutez une variable de niveau classe pour stocker une référence dans **NotificationRegistrationService** et le **PushTemplate** générique :

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Dans le même fichier, mettez à jour la fonction **didRegisterForRemoteNotificationsWithDeviceToken** pour initialiser **NotificationRegistrationService** avec les paramètres requis, puis appelez la fonction **register**.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Pour simplifier, vous allez utiliser quelques instructions d’impression pour mettre à jour la fenêtre de sortie avec le résultat de l’opération **register**.

1. Maintenant, générez et exécutez l’application sur un appareil physique. Vous devez voir « Inscrit » dans la fenêtre de sortie.

## <a name="test-the-solution"></a>Test de la solution

Notre application à ce stade est inscrite avec **NotificationHub** et peut recevoir des notifications push. Dans Xcode, arrêtez le débogueur, puis fermez l’application si elle est en cours d’exécution. Ensuite, vérifiez que les informations **Installation de périphérique** sont comme prévues et que notre application peut désormais recevoir des notifications push.  

### <a name="verify-the-device-installation"></a>Vérifier l’installation de l’appareil

Vous pouvez maintenant faire la même demande que précédemment à l’aide de **Postman** pour [vérifier le jeton SAS](#verify-the-sas-token). En supposant que le jeton SAS n’a pas expiré, la réponse doit inclure désormais les détails d’installation fournis, tels que les balises et les modèles.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Si votre précédent **jeton SAS** a expiré, vous pouvez ajouter un **point d'arrêt** à la **ligne 24** de la classe **TokenUtility** afin d'obtenir un nouveau **jeton SAS** et de mettre à jour l’en-tête **Autorisation** avec cette nouvelle valeur.

### <a name="send-a-test-notification-azure-portal"></a>Envoyer une notification de test (Portail Azure)

Le moyen le plus rapide pour tester que vous pouvez recevoir des notifications consiste à parcourir le Hub de notification dans le portail Azure :

1. Dans le portail Azure, accédez à l’onglet **Vue d’ensemble** sur votre hub de notification.

1. Sélectionnez **Test d’envoi**, au-dessus du résumé **Essentials** dans le coin supérieur gauche de la fenêtre du portail :

    ![Bouton Test d’envoi du Résumé Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Choisissez **Modèle personnalisé** dans la liste **Plateformes**.

1. Entrez **12345** pour **Envoi à l’expression de balise**. Vous aviez précédemment spécifié cette balise dans notre installation.

1. Vous pouvez également modifier le **message** dans la charge utile JSON :

    ![Test d’envoi Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Sélectionnez **Envoyer**. Le portail doit indiquer si la notification a été correctement envoyée à l’appareil :

    ![Résultats du test d’envoi Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    En supposant que l’application n’est pas en cours d’exécution au premier plan, vous devez également voir une notification dans le **Centre de notifications** sur votre appareil. En appuyant sur la notification, vous devriez ouvrir l’application et afficher l’alerte.

    ![Exemple de Notification reçue](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Envoyer une notification de test (opérateur de messagerie)

Vous pouvez envoyer des notifications via l’[API REST](/rest/api/notificationhubs/) à l’aide de **Postman**, ce qui peut être un moyen de test plus pratique.

1. Ouvrez un nouvel onglet dans **Postman**.

1. Définissez la requête sur **POST** et entrez l’adresse suivante :

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configurez les en-têtes de requête comme suit :

   | Clé                            | Valeur                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Autorisation                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | Balises                           | "12345"                        |

1. Configurez la requête **BODY** à utiliser **RAW - JSON (application.json)** avec la charge utile JSON suivante :

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Sélectionnez le bouton **Code** qui apparaît dans le coin supérieur droit sous le bouton **Enregistrer**. La requête doit ressembler à l’exemple suivant :

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Sélectionnez le bouton **Envoyer**.

Vous devez obtenir un code d’état de réussite **201 Créé** et recevoir la notification sur l’appareil client.

## <a name="next-steps"></a>Étapes suivantes
Vous disposez maintenant d’une application Swift iOS de base connectée à un hub de notification via l’[API REST](/rest/api/notificationhubs/) et qui peut envoyer et recevoir des notifications. Pour plus d’informations, consultez les articles suivants :

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
