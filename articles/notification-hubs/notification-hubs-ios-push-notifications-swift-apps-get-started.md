---
title: Notifications push vers les applications iOS Swift à l’aide d’Azure Notification Hubs | Microsoft Docs
description: Découvrez comment envoyer des notifications vers des applications iOS Swift à l’aide d’Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994765"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Didacticiel : Notifications Push aux applications iOS Swift à l’aide de l’API REST de Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Dans ce didacticiel, vous utilisez Azure Notification Hubs pour envoyer des notifications push à une application iOS basée sur Swift à l’aide du [API REST](/rest/api/notificationhubs/). Vous créez une application iOS vide qui reçoit des notifications Push à l’aide du [service de notification Push Apple (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Dans ce didacticiel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Génération du fichier de demande de signature de certificat
> * Inscription de votre application pour les notifications Push
> * Création d’un profil d’approvisionnement pour l’application
> * Création d’un hub de notifications
> * Configurer le concentrateur de notification avec les informations d’APNS
> * Connexion de votre application iOS aux hubs de notification
> * Tester la solution

## <a name="prerequisites"></a>Conditions préalables
Pour suivre l’article, vous devez préalablement :

- Passez en revue [vue d’ensemble d’Azure Notification Hubs](notification-hubs-push-notification-overview.md) si vous n’êtes pas familiarisé avec le service. 
- En savoir plus sur les enregistrements et l’installation : [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md)
- Un actif [compte de développeur Apple](https://developer.apple.com) 
- Un Mac avec Xcode, ainsi que d’un certificat de développeur valide installé dans votre trousseau de clés
- Un appareil physique iPhone vous pouvez exécuter et déboguer avec (il n’est pas possible de tester les notifications push avec le simulateur)
- Votre appareil physique iPhone inscrit dans le [portail Apple](https://developer.apple.com) et associé à votre certificat
- Un [abonnement Azure](https://portal.azure.com) où vous pouvez créer et gérer les ressources

Il doit être possible de suivre la procédure pour créer cet exemple de principes de la première sans expérience préalable. Toutefois, vous êtes familiarisé avec les concepts suivants seront utile :

- Générer des applications iOS avec Xcode et Swift
- Configuration d’un [Azure Notification Hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) pour iOS
- Vous êtes familiarisé avec la [portail des développeurs Apple](https://developer.apple.com) et [portail Azure](https://portal.azure.com)

> [!NOTE]
> Le hub de notification sera configuré pour utiliser le *bac à sable* mode d’authentification. Vous ne devez pas utiliser ce mode d’authentification pour les charges de travail de production.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connexion de votre application iOS à Notification Hubs
Dans cette section, vous allez générer l’application iOS qui se connecte au hub de notification.  

### <a name="create-an-ios-project"></a>Créer un projet iOS
1. Dans **Xcode**, créez un projet iOS et sélectionnez le **Single View Application** modèle.
2. Lorsque vous définissez les options pour le nouveau projet, procédez comme suit :
    1. Utiliser le même **Product Name** (autrement dit, **PushDemo**) et **identificateur d’organisation** (autrement dit, `com.<organization>`) que vous avez utilisé lors le **Bundle Identificateur** a été défini dans le **portail des développeurs Apple**. 
    2. Choisissez le **équipe** qui le **ID d’application** a été configurée pour.
    3. Définir le **langage** à **Swift**.
    4. Cliquez sur **Suivant**
3. Créez un dossier appelé **SupportingFiles**.
4. Créer un nouveau **plist** fichier appelé **devsettings.plist** sous le **SupportingFiles** dossier. Veillez à ajouter ce dossier à votre **gitignore** fichier afin qu’il n’est pas validée lorsque vous travaillez avec un **référentiel git**. Dans une application de production, vous serait probablement conditionnellement la configuration ces secrets dans le cadre d’un processus de génération automatisé. Toutefois, il n’est pas couvert dans le cadre de cette procédure pas à pas.
5. Mise à jour **devsettings.plist** pour inclure les entrées de configuration suivantes (à l’aide de vos propres valeurs à partir de la **Hub de Notification** vous approvisionné) :

   | Clé                            | Type                     | Valeur                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Vous pouvez trouver les valeurs requises en accédant à la **Hub de Notification** ressource dans le **Azure portal**. Vous pouvez trouver la **notificationHubName** et le **notificationHubNamespace** valeurs dans le coin supérieur droit de la **Essentials** résumé dans le  **Vue d’ensemble** page.

   ![Résumé de notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Vous pouvez trouver la **notificationHubKeyName** et **notificationHubKey** valeurs en accédant à **des stratégies d’accès**, puis cliquer sur respectifs **accès Stratégie**. Par exemple : `DefaultFullSharedAccessSignature`. Puis copiez à partir de la **chaîne de connexion principale** précédés de la valeur `SharedAccessKeyName=` pour `notificationHubKeyName` et précédés de la valeur `SharedAccessKey=` pour le `notificationHubKey`. La chaîne de connexion doit être au format suivant :
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Pour plus de simplicité, vous allez utiliser *DefaultFullSharedAccessSignature* afin de pouvoir utiliser le jeton pour envoyer des notifications ainsi. Toutefois, dans la pratique la `DefaultListenSharedAccessSignature` serait un meilleur choix pour les situations où vous souhaitez uniquement recevoir des notifications.       
6. Sous **Project Navigator**, cliquez sur le **nom_projet**, puis cliquez sur le **général** onglet
7. Rechercher **identité**, puis définissez le **identificateur de Bundle** valeur afin qu’il corresponde à celui utilisé pour le **ID d’application** (à partir de l’étape précédente), autrement dit, `'com.<organization>.PushDemo'`
8. Rechercher **signature**, puis assurez-vous que vous sélectionnez l’option appropriée **équipe** pour votre **compte de développeur Apple** (celui sous lequel vous avez créé vos certificats et les profils plus haut).  **Xcode** doit extraire automatiquement approprié **profil de provisionnement** selon le **identificateur de Bundle**. Si vous ne voyez pas la nouvelle **profil de provisionnement**, essayez d’actualiser les profils pour le **identité de signature** (*Xcode > Préférences > compte > Afficher les détails*). En cliquant sur le **identité de signature**, puis en cliquant sur le **Actualiser** bouton dans l’angle inférieur droit doit télécharger les profils.
9. Sélectionnez le **fonctionnalités** onglet et vous assurer que **notifications push** sont activés.
10. Ouvrez votre **AppDelegate.swift** fichier pour implémenter le *UNUserNotificationCenterDelegate* de protocole et ajoutez le code suivant au début de la classe :
    
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
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
        
        ...
    }
    ```

    Vous utiliserez ultérieurement ces membres. Le *balises* et *genericTemplate* sera utilisé dans le cadre de l’inscription. Pour plus d’informations sur les balises, consultez [balises pour les inscriptions](notification-hubs-tags-segment-push-message.md) et [des inscriptions de modèle](notification-hubs-templates-cross-platform-push-messages.md).
 
11. Dans le même fichier, ajoutez le code suivant dans le *didFinishLaunchingWithOptions* (fonction) :

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

    Ce code récupère les valeurs de paramètre de **devsettings.plist**, définit le **AppDelegate** classe en tant que le *UNUserNotificationCenter* déléguer, l’autorisation de demandes pour les notifications push, puis appelle *registerForRemoteNotifications*.
    
    Pour plus de simplicité, le code prend en charge **iOS 10 et ultérieures uniquement**. Vous pouvez ajouter la prise en charge pour les versions antérieures du système d’exploitation par manière conditionnelle à l’aide de l’API et les approches respectifs comme vous le feriez normalement.

12. Dans le même fichier, ajoutez les fonctions suivantes :

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Le code utilise le *installationId* et *pushChannel* à inscrire avec le **Hub de Notification**. Dans ce cas, vous utilisez *UIDevice.current.identifierForVendor* pour nous fournir une valeur unique pour identifier le périphérique et ensuite mettre en forme le *deviceToken* pour nous fournir la souhaitée*pushChannel* valeur. Le *showAlert* fonction consiste simplement à afficher du texte de message à des fins de démonstration.

13. Toujours dans **AppDelegate.swift**, ajoutez le *willPresent* et *didReceive* **UNUserNotificationCenterDelegate** fonctionne afficher une alerte lorsque la notification est arrivé lorsque l’application est en cours d’exécution dans le premier plan et arrière-plan respectivement
    
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

14. Ajouter des instructions print vers le bas de la *didRegisterForRemoteNotificationsWithDeviceToken* (fonction) pour vérifier que *installationId* et *pushChannel* sont en cours valeurs affectées
15. Créer des dossiers (**modèles**, **Services**, et **utilitaires**) pour les composants fondamentaux, vous allez ajouter au projet plus tard
16. Vérifiez que le projet généré et s’exécute sur un appareil physique (push notifications ne peuvent pas être testées à l’aide du simulateur)

### <a name="create-models"></a>Créer des modèles
Dans cette étape, vous allez créer un ensemble de modèles pour représenter le [API REST Notification Hubs](/rest/api/notificationhubs/) charges utiles et pour stocker le texte requis **jeton SAS** données.


1.  Ajouter un nouveau fichier swift à la **modèles** appelé **PushTemplate.swift**. Ce modèle fournit un struct qui représente le **corps** d’un modèle individuel dans le cadre de la **DeviceInstallation** charge utile :
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Ajouter un nouveau fichier swift à la **modèles** dossier nommé **DeviceInstallation.swift**. Ce fichier définit un struct qui représente la charge utile pour la création ou la mise à jour un **Installation de périphérique**. Ajoutez le code suivant au fichier :
    
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

3.  Ajoutez un nouveau fichier swift sous **modèles** appelé **TokenData.swift**. Ce modèle permet de stocker un **jeton SAS** , ainsi que son expiration.

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

### <a name="generate-a-sas-token"></a>Générer un jeton SAP
**Notification Hubs** utilisent la même infrastructure de sécurité en tant que **Azure Service Bus**. Pour appeler l’API REST, vous devrez [générer par programmation un jeton SAP](/rest/api/eventhub/generate-sas-token) qui peut être utilisé dans le **autorisation** en-tête de la requête.  

Le jeton résultant sera dans le format suivant : 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Le processus lui-même implique les étapes clés six mêmes :  

1.  L’expiration de l’informatique [heure d’époque UNIX](https://en.wikipedia.org/wiki/Unix_time) format (secondes écoulées depuis 00:00:00 UTC 1er janvier 1970)
2.  Mise en forme le **ResourceURL ne** (représentant la ressource que vous essayez d’accéder, autrement dit, `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) il est encodée en pourcentage et en minuscules
3.  Préparation de la **StringToSign**, qui est composé de `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Informatique (et un codage base 64) le **Signature** à l’aide de la **HMAC-SHA256** de la **StringToSign** valeur avec le **clé** dans le cadre de la **Chaîne de connexion** (pour respectifs **règle d’autorisation**)
5.  Mise en forme le codée en base 64 **Signature** donc il est encodés de pourcentage
6.  Construction de la **jeton** dans le format attendu à l’aide de la **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**et **UrlEncodedResourceUrl** valeurs

Consultez le [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md) pour obtenir une présentation plus poussée de **Signature d’accès partagé** et comment il est utilisé par **Azure Service Bus** et  **Notification Hubs**.

Dans le cadre de cet exemple Swift, vous allez utiliser open source d’Apple **CommonCrypto** bibliothèque pour faciliter le hachage de la signature. Comme il est une bibliothèque C, il n’est pas accessible dans Swift out-of-the-box. Toutefois, vous pouvez rendre disponible à l’aide d’un en-tête de pontage. Pour ajouter et configurer l’en-tête de pontage :

1. Dans **Xcode**, accédez à **fichier**, puis **New**, puis **fichier** et sélectionnez **fichier d’en-tête** Nommez-le *'BridgingHeader.h'*
2. Modifier le fichier à importer **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Mettre à jour la cible **paramètres de Build** pour référencer l’en-tête de pontage. Ouvrez le **des paramètres** onglet et faites défiler jusqu'à la **compilateur Swift** section. Vérifiez que le **installer Objective-C compatibilité en-tête** option définie sur **Oui** et entrez le chemin d’accès à notre en-tête de pontage dans le **en-tête de pontage Objective-C**   option qui est `'\<ProjectName\>/BridgingHeader.h'`. Si vous ne trouvez pas ces options, assurez-vous d’avoir le **tous les** affichage sélectionné (au lieu de **base** ou **personnalisé**).
    
   Il existe de nombreuses bibliothèques de tiers open source wrapper disponibles, ce qui peuvent rendre à l’aide de **CommonCrypto** un peu plus facile. Il est abordée dans cet article.

4. Ajoutez un nouveau fichier Swift sous le **utilitaires** dossier appelé **TokenUtility.swift** et ajoutez le code suivant :

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
    
    Cet utilitaire encapsule la logique chargée de générer la **jeton SAS**. Le *getSasToken* fonction orchestre les étapes requises pour préparer le jeton, comme indiqué précédemment et sera appelée par le service d’installation dans les étapes décrites plus loin dans ce didacticiel. Les deux autres fonctions sont appelées par le *getSasToken fonction*; *sha256HMac* pour calculer la signature et *urlEncodedString* pour l’encodage de la chaîne d’Url respectif. Le *urlEncodedString* fonction était nécessaire car il n’a pas été possible d’atteindre la sortie requise à l’aide intégrée *addingPercentEncoding* (fonction). Le [iOS Azure Storage SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) pris en charge comme un excellent exemple de comment aborder ces opérations bien que dans Objective-C. Plus d’informations sur **jetons SAP Azure Service Bus** se trouve dans le [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Vérifier le jeton SAS
Avant d’implémenter le service d’installation dans le client, vous pouvez vérifier que notre application génère correctement le **jeton SAS** à l’aide de votre utilitaire http de choix. Dans le cadre de cet article, notre outil de choix sera **Postman**.

Prenez note de la *installationId* et *jeton* valeurs générés par l’application à l’aide de placés correctement imprimer l’instruction ou le point d’arrêt. 

Suivez ces étapes pour appeler le *installations* API :

1. Dans **Postman**, ouvrez un nouvel onglet
2. Affecter à la requête **obtenir** et l’adresse suivante :

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Configurez les en-têtes de demande comme suit :
    
   | Clé           | Valeur            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Cliquez sur le **Code** bouton (en haut à droite sous le **enregistrer** bouton). La demande doit ressembler à l’exemple ci-dessous :

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Cliquez sur le **envoyer** bouton

Aucun enregistrement n’existe pour le texte spécifié *installationId* à ce stade, toutefois cela doit entraîner une **404 introuvable** réponse plutôt que **401 non autorisé**. Ce résultat doit confirmer que le **jeton SAS** a été acceptée.

### <a name="implement-the-installation-service-class"></a>Implémentez la classe de service d’installation
Ensuite, vous allez implémenter notre base wrapper autour de la [Installations REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Ajoutez un nouveau fichier Swift sous le **Services** dossier appelé **NotificationRegistrationService.swift**, puis ajoutez le code suivant à ce fichier :

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
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
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
 
Les détails requis sont fournis en tant que partie de l’initialisation. Balises et les modèles sont éventuellement passés dans le *inscrire* fonction faisant partie de la **Installation de périphérique** charge utile JSON.  

Le *inscrire* fonction appelle d’autres fonctions privées pour préparer la demande. Une fois qu’une réponse est reçue, la saisie semi-automatique est appelée indiquant si l’inscription a réussi ou non.  

Le point de terminaison demande est construit par la *getBaseAddress* à l’aide de la fonction le **Hub de Notification** paramètres **espace de noms** et **nom**fourni pendant l’initialisation.  

Le *getSasToken* fonction vérifie si le jeton actuellement stocké est valide, sinon il appelle le **TokenUtility** pour générer un nouveau et le stocker avant de retourner une valeur. 

Enfin le *encodeToJson* convertira les objets de modèle en question en JSON pour une utilisation en tant que partie intégrante du corps de demande.

### <a name="invoke-the-notification-hubs-rest-api"></a>Appeler l’API REST de Notification Hubs
La dernière étape consiste à mettre à jour **AppDelegate** à utiliser le **NotifiationRegistrationService** à inscrire auprès de notre **NotificationHub**. 

1. Ouvrez **AppDelegate.swift** et ajoutez une variable de niveau classe pour stocker une référence à la **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. Dans le même fichier, mettez à jour le *didRegisterForRemoteNotificationsWithDeviceToken* fonction pour initialiser le **NotificationRegistrationService** avec les paramètres requis, puis appelez le *inscrire* (fonction).

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Pour plus de simplicité, vous allez utiliser quelques instructions print pour mettre à jour de la fenêtre de sortie avec le résultat de la *inscrire* opération. 

3. Maintenant, générez et exécutez l’application (sur un appareil physique). Vous devriez voir **« Inscrit »** dans la fenêtre Sortie.

## <a name="test-the-solution"></a>Tester la solution
À ce stade, notre application est inscrite avec **NotificationHub** et il peut recevoir des notifications push. Dans **Xcode**, arrêtez le débogueur et fermer l’application (si elle est en cours d’exécution). Ensuite, vous allez vous assurer que le **Installation de périphérique** détails sont comme prévu et que notre application peut en effet maintenant recevoir des notifications push.  

### <a name="verify-the-device-installation"></a>Vérifier l’installation de l’appareil
Vous pouvez maintenant apporter la même demande comme vous l’avez fait précédemment à l’aide **Postman** pour [vérifier le jeton SAS](#verify-the-sas-token). En supposant que le **jeton SAS** n’a pas expiré, la réponse doit inclure désormais les détails d’installation que vous avez fourni comme les modèles et les balises.  

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

### <a name="send-a-test-notification-azure-portal"></a>Envoyer une notification de test (portail Azure)
Le moyen le plus rapide pour tester que vous pouvez recevoir des notifications consiste à accéder à la **Hub de Notification** dans le **Azure portal**.

1. Dans le **Azure portal**, accédez à la **vue d’ensemble** onglet sur votre **Hub de Notification**
2. Cliquez sur **Test d’envoi** (en haut à gauche) au-dessus de la **Essentials** résumé

    ![Notification Hubs Essentials Test résumé bouton d’envoi](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Choisissez **modèle personnalisé** dans la liste des **plateformes**
4. Entrez **12345** pour le **envoyer à l’Expression de balise** (vous aviez spécifié de cette balise dans notre installation)
5. Si vous le souhaitez, modifiez le **message** dans la charge utile JSON
    
    ![Envoi de Test de Hubs de notification](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Cliquez sur **envoyer** et le portail doit indiquer si la notification a été envoyée à l’appareil

    ![Envoyer les résultats de Test de Hubs de notification](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Vous devriez également voir une notification dans le **centre de notifications** sur votre appareil (en supposant que l’application n’est pas en cours d’exécution au premier plan). Appuyant sur la notification doit ouvrir l’application et afficher l’alerte.

    ![Notification reçue exemple](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Envoyer une notification de test (Postman)
Vous pouvez envoyer des notifications via respectifs [API REST](/rest/api/notificationhubs/) via **Postman** as ainsi il peuvent être un moyen plus pratique pour tester. 

1. Dans **Postman**, ouvrez un nouvel onglet
2. Affecter à la requête **POST** et entrez l’adresse suivante :

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Configurez les en-têtes de demande comme suit :
    
   | Clé                            | Valeur                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | modèle                       |
   | Balises                           | "12345"                        |

4. Configurez la requête **corps** à utiliser **RAW - JSON (application.json)** avec la charge utile JSON suivante :

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Cliquez sur le **Code** bouton (en haut à droite sous le **enregistrer** bouton). La demande doit ressembler à l’exemple ci-dessous :

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

5. Cliquez sur le **envoyer** bouton

Vous devez obtenir un code d’état de réussite et recevoir la notification sur l’appareil client.

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant une application Swift base iOS connectée à un **Hub de Notification** via la [API REST](/rest/api/notificationhubs/) et peut envoyer et recevoir des notifications. Pour plus d’informations, consultez les articles suivants : 

- [Vue d’ensemble des concentrateurs de Notification Azure](notification-hubs-push-notification-overview.md)
- [API REST de Notification Hubs](/rest/api/notificationhubs/)
- [SDK de concentrateurs de notification pour les opérations de serveur principal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK sur GitHub](https://github.com/Azure/azure-notificationhubs)
- [Inscrire avec le serveur principal d’application](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md)
- [Utilisation des balises](notification-hubs-tags-segment-push-message.md) 
- [Utilisation de modèles personnalisés](notification-hubs-templates-cross-platform-push-messages.md)
- [Contrôle d’accès de service Bus avec des Signatures d’accès partagé](../service-bus-messaging/service-bus-sas.md)
- [Générer des jetons SAP par programme](/rest/api/eventhub/generate-sas-token)
- [Sécurité d’Apple : chiffrement commun](https://developer.apple.com/security/)
- [Heure d’époque UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
