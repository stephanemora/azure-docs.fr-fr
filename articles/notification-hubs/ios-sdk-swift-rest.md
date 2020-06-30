---
title: Envoyer des notifications Push vers des applications iOS Swift avec Azure Notification Hubs et des API REST
description: Dans ce tutoriel, vous allez découvrir comment utiliser Azure Notification Hubs et les API REST pour envoyer des notifications Push à des appareils iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127263"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Tutoriel : Envoyer des notifications Push vers des applications iOS Swift à l’aide des API REST Notification Hubs

Ce tutoriel montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS à l’aide des API REST.

Ce tutoriel couvre les étapes suivantes :

- Créer un exemple d’application iOS
- Connecter votre application iOS à Azure Notification Hubs
- Envoyer des notifications Push de test
- Vérifier la réception des notifications par votre application

Vous pouvez télécharger le code complet de ce tutoriel à partir de [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin de ce qui suit :

- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau

- Un iPhone ou un iPad exécutant iOS version 10 ou ultérieure

- Votre appareil physique inscrit auprès du [portail Apple](https://developer.apple.com/) et associé à votre certificat

Avant de continuer, veillez à suivre le tutoriel précédent sur la prise en main d’[Azure Notification Hubs pour les applications iOS](https://go.microsoft.com/fwlink/?linkid=2129801) afin de configurer les informations d’identification Push dans votre hub de notification. Même si vous n’avez aucune expérience préalable avec le développement sur iOS, vous devriez être en mesure d’effectuer ces étapes.

> [!NOTE]
> En raison des conditions de configuration requises pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil iOS physique (iPhone ou iPad) au lieu de l’émulateur iOS.

Dans les sections suivantes, vous allez créer une application iOS qui se connecte au hub de notification.

## <a name="create-an-ios-project"></a>Créer un projet iOS

1. Dans Xcode, créez un projet iOS et sélectionnez le modèle  **Single View Application** (Application mono-vue).

2. Lorsque vous définissez les options pour le nouveau projet :
   - Spécifiez le  **nom du produit** (PushDemo) et l’ **identificateur de l’organisation** (`com.<organization>`) que vous avez utilisé quand vous avez défini l’**identificateur de bundle** dans le portail des développeurs Apple.
   - Choisissez l’ **équipe** pour laquelle l’ **ID d’application** a été configuré.
   - Choisissez  **Swift** comme  **langage**.
   - Sélectionnez  **Suivant**.

3. Créez un dossier nommé **SupportingFiles**.

4. Créez un fichier p-list nommé **devsettings.plist** dans le dossier **SupportingFiles**. Veillez à ajouter ce dossier à votre fichier **gitignore** afin qu’il ne soit pas validé quand vous travaillez avec un dépôt Git. Dans une application de production, vous pourriez configurer ces secrets de façon conditionnelle dans le cadre d’un processus de création automatisé. Ces paramètres ne sont pas traités dans ce tutoriel.

5. Mettez à jour **devsettings.plist** de façon à inclure les entrées de configuration suivantes à l’aide de vos propres valeurs à partir du hub de notification que vous avez provisionné :

   | **Clé**                  | **Type** | **Valeur**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | notificationHubKeyName   | String   | `<hubKeyName>`   |
   | notificationHubName      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   Vous pouvez trouver les valeurs requises en accédant aux ressources du hub de notification dans le portail Azure. Plus particulièrement, les valeurs **notificationHubName** et **notificationHubNamespace** se trouvent en haut à droite du résumé **Bases** dans la page **Vue d’ensemble**.

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Valeurs requises":::

   Vous pouvez également trouver les valeurs **notificationHubKeyName** et **notificationHubKey**  en accédant à **Stratégies d’accès** et en sélectionnant la **stratégie d’accès** correspondante, par exemple **DefaultFullSharedAccessSignature**. Après cela, copiez à partir de la **chaîne de connexion principale** la valeur préfixée avec `SharedAccessKeyName=` pour **notificationHubKeyName**, et la valeur préfixée avec `SharedAccessKey=` pour **notificationHubKey**. La chaîne de connexion doit être dans ce format :

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Pour faire simple, spécifiez **DefaultFullSharedAccessSignature**, afin de pouvoir utiliser le jeton pour envoyer des notifications. Dans la pratique, **DefaultListenSharedAccessSignature** est un meilleur choix pour les situations dans lesquelles vous souhaitez uniquement recevoir des notifications.

6. Sous **Project Navigator**, sélectionnez le **nom du projet** puis sélectionnez l’onglet **General** .

7. Recherchez  **Identity** puis définissez la valeur **Bundle Identifier** pour qu’elle correspondre à  `com.<organization>.PushDemo`, qui est la valeur utilisée pour l’ **ID de l’application** lors d’une étape précédente.

8. Recherchez **Signature et fonctionnalités**, puis sélectionnez l’ **équipe** correspondant à votre  **Compte de développeur Apple**. La valeur  **Team** doit correspondre à celle sous laquelle vous avez créé vos certificats et vos profils.

9. Xcode doit télécharger automatiquement la valeur **Provisioning Profile** appropriée en fonction du **Bundle Identifier**. Si vous ne voyez pas la nouvelle valeur **Provisioning Profile** , essayez d’actualiser les profils pour l’ **identité de signature** en sélectionnant **Xcode**, **Preferences**, **Account**, puis sélectionnez le bouton **Download Manual Profiles** pour télécharger les profils.

10. Toujours sous l’onglet **Signing & Capabilities** , cliquez sur le bouton **+ Capability** et appuyez deux fois sur **Push Notifications** dans la liste pour être sûr que les **notifications push** sont activées.

11. Ouvrez votre fichier **AppDelegate.swift** pour implémenter le protocole **UNUserNotificationCenterDelegate** et ajoutez le code suivant en haut de la classe :

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

    Vous utiliserez les membres plus tard. Plus précisément, vous allez utiliser le membre **tags** dans le cadre de l’inscription à l’aide d’un **modèle personnalisé**. Pour plus d’informations sur les balises, consultez  [Balises pour les inscriptions](notification-hubs-tags-segment-push-message.md) et [Inscriptions de modèles](notification-hubs-templates-cross-platform-push-messages.md).

12. Dans le même fichier, ajoutez le code suivant à la fonction **didFinishLaunchingWithOptions** :

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

    Ce code récupère les paramètres du fichier **devsettings.plist**, définit la classe **AppDelegate** comme délégué **UNUserNotificationCenter** , demande l’autorisation d’envoyer des notifications Push, puis appelle **registerForRemoteNotifications**.

    Par souci de simplicité, le code prend en charge uniquement iOS 10 et versions ultérieures. Vous pouvez ajouter la prise en charge de versions iOS antérieures en utilisant de façon conditionnelle les API et approches respectives, comme vous le feriez normalement.

13. Dans le même fichier, ajoutez le code suivant :

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

    Ce code utilise les valeurs **installationId** et **pushChannel** pour l’inscription auprès du hub de notification. Dans le cas présent, vous utilisez **UIDevice.current.identifierForVendor** pour fournir une valeur unique et identifier l’appareil, puis vous formatez le **deviceToken** pour fournir la valeur **pushChannel** souhaitée. La fonction **showAlert** existe simplement pour afficher quelques messages textuels à des fins de démonstration.

14. Toujours dans le fichier **AppDelegate.swift** , ajoutez les fonctions **willPresent** et **didReceive** à **UNUserNotificationCenterDelegate**. Ces fonctions affichent une alerte quand elles sont informées qu’une application s’exécute en premier plan ou en arrière-plan.

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

15. Ajoutez des instructions   en bas de la fonction **didRegisterForRemoteNotificationsWithDeviceToken** pour vérifier que des valeurs sont attribuées à **installationId** et **pushChannel** :

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Créez les dossiers **Models**, **Services** et **Utilities** pour les composants fondamentaux que vous ajouterez au projet plus tard.

17. Vérifiez que le projet se crée et s’exécute sur un appareil physique. Les notifications Push ne peuvent pas être testées à l’aide du simulateur.

## <a name="create-models"></a>Créer des modèles

Lors de cette étape, vous allez créer un ensemble de modèles pour représenter les charges utiles de l’ [API REST Notification Hubs](/rest/api/notificationhubs/) et pour stocker les données de jeton de signature d’accès partagé (SAS) nécessaires.

1. Ajoutez un nouveau fichier Swift nommé **PushTemplate.swift** au dossier **Models**. Ce modèle définit une structure qui représente le **BODY** d’un modèle individuel dans le cadre de la charge utile **DeviceInstallation**.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Ajoutez un nouveau fichier Swift nommé **DeviceInstallation.swift** au dossier **Models**. Ce fichier définit une structure qui représente la charge utile de création ou de mise à jour d’une **Installation d’appareil**. Ajoutez le code suivant au fichier :

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

3. Ajoutez un nouveau fichier Swift nommé **TokenData.swift** au dossier  **Models**. Ce modèle est utilisé pour stocker un jeton SAS ainsi que son expiration. Ajoutez le code suivant au fichier :

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

## <a name="generate-a-sas-token"></a>Générer un jeton SAS

Notification Hubs utilise la même infrastructure de sécurité qu’Azure Service Bus. Pour appeler l’API REST, [générez programmatiquement un jeton SAS](/rest/api/eventhub/generate-sas-token) qui peut être utilisé dans l’en-tête **Authorization** de la requête.

Le jeton obtenu présente le format suivant :

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Le processus implique les six mêmes étapes :

1. Calculer l’expiration au format  [heure (époque) UNIX](https://en.wikipedia.org/wiki/Unix_time), ce qui signifie le nombre de secondes écoulées depuis le 1er janvier 1970 à minuit (temps universel coordonné).

2. Mettre en forme le **ResourceUrl** qui représente la ressource à laquelle vous tentez d’accéder pour qu’elle soit encodée en pourcentage et en minuscules. Le format de **ResourceUrl** est `https://<namespace>.servicebus.windows.net/<hubName>`.

3. Préparer  **StringToSign**, dont le format est `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`.

4. Calculer et coder en base64 la **Signature** à l’aide du hachage HMAC-SHA256 de la valeur **StringToSign**. La valeur de hachage est utilisée avec la partie **Key** de la  **chaîne de connexion** pour la  **règle d’autorisation**correspondante.

5. Mettre en forme la **Signature** encodée en Base64 pour qu’elle soit encodée en pourcentage.

6. Construire le jeton au format attendu avec les valeurs **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName** et **UrlEncodedResourceUrl** .

Consultez la [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md) pour obtenir plus d’informations sur les signatures d’accès partagé et comment Azure Service Bus et Notification Hubs l’utilisent.

Dans le cadre de cet exemple Swift, vous utilisez la bibliothèque open source d’Apple  **CommonCrypto** pour faciliter le hachage de la signature. Étant donné qu’il s’agit d’une bibliothèque C, elle n’est pas immédiatement accessible en Swift. Vous pouvez rendre disponible la bibliothèque à l’aide d’un en-tête de pontage.

Pour ajouter et configurer l’en-tête de pontage :

1. Dans Xcode, sélectionnez **File**,  **New**,  **File**, puis  **Header File**. Nommez le fichier d’en-tête **BridgingHeader.h**.

2. Modifiez le fichier à importer **CommonHMAC.h** :

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Mettez à jour les **Build Settings** (paramètres de build) de la cible pour référencer l’en-tête de pontage :

   1. Sélectionnez le projet **PushDemo** et faites défiler jusqu’à la section **Swift Compiler**.

   2. Veillez à ce que l’option  **Installer l’en-tête de compatibilité Objective-C**  soit définie sur  **Oui**.

   3. Entrez le chemin `<ProjectName>/BridgingHeader.h` dans l’option **Objective-C bridging Header**. Il s’agit du chemin vers l’en-tête de pontage.

   Si vous ne trouvez pas ces options, vérifiez que vous avez sélectionné la vue **All**  plutôt que  **Basic** ou **Customized**.

   Il existe de nombreuses bibliothèques wrapper tierces open source disponibles pouvant simplifier l’utilisation de  **CommonCrypto**. Toutefois, nous ne discuterons pas de ces bibliothèques dans cet article.

4. Ajoutez un nouveau fichier Swift nommé **TokenUtility.swift** dans le dossier **Utilities** et ajoutez le code suivant :

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

   Comme indiqué précédemment, la fonction **getSasToken** orchestre les étapes requises pour préparer le jeton. La fonction sera appelée par le service d’installation plus loin dans ce didacticiel.

   Les deux autres fonctions sont appelées par la fonction **getSasToken** :  **sha256HMac** pour calculer la signature et **urlEncodedString** pour encoder la chaîne URL associée. La fonction **urlEncodedString** est nécessaire car il n’est pas possible d’atteindre la sortie requise en utilisant la fonction intégrée  **addingPercentEncoding**.

   Le  [SDK Stockage Azure iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) est un excellent exemple illustrant comment aborder ces opérations en Objective-C. Vous trouverez plus d’informations sur les jetons SAS Azure Service Bus dans la [documentation Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

5. Dans **AppDelegate.swift**, ajoutez le code suivant à la fonction `didRegisterForRemoteNotificationsWithDeviceToken` pour vérifier que **TokenUtility.getSasToken** génère un jeton valide.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Veillez à remplacer les valeurs d’espace réservé de la chaîne **baseAddress** par vos propres valeurs.

## <a name="verify-the-sas-token"></a>Vérifier le jeton SAS

Avant d’implémenter le service d’installation dans le client, vérifiez que notre application génère correctement le jeton SAS à l’aide d’un utilitaire HTTP. Dans le cadre de ce tutoriel, nous avons choisi d’utiliser **Postman**.

Prenez note des valeurs **installationId** et **token** générées par l’application.

Effectuez ces étapes pour appeler l’API **d’installations**  :

1. Dans **Postman**, ouvrez un nouvel onglet.
2. Définissez la requête sur **GET** et spécifiez l’adresse suivante :

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Configurez les en-têtes de requête comme suit :

   | **Clé**       | **Valeur**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorisation | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Sélectionnez le bouton **Code** qui apparaît dans le coin supérieur droit sous le bouton **Save**. La requête doit ressembler à l’exemple suivant :

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Sélectionnez le bouton **Send**.

Aucun enregistrement n’existe pour la valeur spécifiée **installationId** à ce stade. La vérification doit aboutir à une réponse « 404 Introuvable » plutôt qu’à une réponse « 401 non autorisé ». Ce résultat doit confirmer que le jeton SAS a été accepté.

## <a name="implement-the-installation-service-class"></a>Implémenter la classe de service d’installation

Ensuite, implémentez un wrapper de base autour de l’ [API REST d’installations](/rest/api/notificationhubs/create-overwrite-installation).

Ajoutez un nouveau fichier Swift nommé **NotificationRegistrationService.swift** dans le dossier **Services**, puis ajoutez le code suivant à ce fichier :

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

Les détails requis sont fournis dans le cadre de l’initialisation. Les balises et les modèles sont éventuellement passés à la fonction **register** pour former une partie de la charge utile JSON d’ **installation d’appareil**.

La fonction **register** appelle les autres fonctions privées pour préparer la requête. Une fois une réponse reçue, l’achèvement est appelé et indique si l’inscription a réussi.

Le point de terminaison de la requête est créé par la fonction **getBaseAddress**. La construction utilise les paramètres du hub de notification *namespace* et *name* fournis lors de l’initialisation.

La fonction **getSasToken** vérifie si le jeton actuellement stocké est valide. Si le jeton n’est pas valide, la fonction appelle **TokenUtility** pour générer un nouveau jeton, puis le stocke avant de retourner une valeur.

Pour finir, **encodeToJson** convertit les objets de modèle en question au format JSON pour une utilisation en tant que partie intégrante du corps de la requête.

## <a name="invoke-the-notification-hubs-rest-api"></a>Invoquer l’API REST de Notification Hubs

La dernière étape est la mise à jour d’ **AppDelegate** pour utiliser **NotificationRegistrationService** afin de s’inscrire auprès du **NotificationHub**

1. Ouvrez **AppDelegate.swift** et ajoutez des variables de niveau classe pour stocker une référence à **NoficiationRegistrationService** et le **PushTemplate** générique :

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. Dans le même fichier, mettez à jour la fonction **didRegisterForRemoteNotificationsWithDeviceToken** pour initialiser **NotificationRegistrationService** avec les paramètres requis, puis appelez la fonction **register**.

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

   Par souci de simplicité, le code utilise des instructions `print` pour mettre à jour la fenêtre de sortie avec le résultat de l’opération **register**.

3. Générez et exécutez l’application sur un appareil physique. Vous devez voir la mention **Registered** dans la fenêtre de sortie.

## <a name="test-the-solution"></a>Test de la solution

À ce stade, l’application est inscrite auprès de **NotificationHub** et peut recevoir des notifications Push. Dans Xcode, arrêtez le débogueur, puis fermez l’application si elle est en cours d’exécution. Ensuite, vérifiez que les détails **d’installation d’appareil** sont comme prévus et que l’application peut désormais recevoir des notifications Push.

### <a name="verify-the-device-installation"></a>Vérifier l’installation de l’appareil

Vous pouvez maintenant effectuer la même requête que précédemment en utilisant **Postman** pour [vérifier le jeton SAS](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). En supposant que le jeton SAS n’a pas expiré, la réponse doit inclure désormais les détails d’installation fournis, tels que les balises et les modèles.

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

Si votre précédent jeton SAS a expiré, vous pouvez ajouter un point d’arrêt à la ligne 24 de la classe  **TokenUtility** afin d’obtenir un nouveau jeton SAS et de mettre à jour l’en-tête **Autorisation** avec cette nouvelle valeur.

### <a name="send-a-test-notification-azure-portal"></a>Envoyer une notification de test (Portail Azure)

Le moyen le plus rapide pour tester que vous pouvez recevoir des notifications consiste à parcourir le Hub de notification dans le portail Azure :

1. Dans le portail Azure, accédez à l’onglet **Vue d’ensemble** sur votre hub de notification.

2. Sélectionnez **Envoi de test**, au-dessus du résumé **Bases** en haut à gauche de la fenêtre du portail :

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Envoi de test du résumé Bases Notification Hubs":::

3. Choisissez **Modèle personnalisé** dans la liste **Plateformes**.

4. Entrez **12345** pour  **Envoyer à l’expression de balise**. Vous aviez précédemment spécifié cette balise dans votre installation.

5. Vous pouvez également modifier le **message** dans la charge utile JSON :

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Envoi de test Notification Hubs":::

6. Sélectionnez **Envoyer**. Le portail doit indiquer si la notification a été correctement envoyée à l’appareil :

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Résultat de l’envoi de test":::

   En supposant que l’application n’est pas en cours d’exécution au premier plan, vous devez également voir une notification dans le **Centre de notifications** sur votre appareil. En appuyant sur la notification, vous devriez ouvrir l’application et afficher l’alerte.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Notification de test":::

### <a name="send-a-test-notification-mail-carrier"></a>Envoyer une notification de test (opérateur de messagerie)

Vous pouvez envoyer des notifications par le biais de [l’API REST](/rest/api/notificationhubs/) à l’aide de  **Postman**, ce qui peut être un moyen de test plus pratique.

1. Ouvrez un nouvel onglet dans **Postman**.

2. Définissez la requête sur  **POST** et entrez l’adresse suivante :

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Configurez les en-têtes de requête comme suit :

   | Clé                           | Valeur                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | Autorisation                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Balises                          | "12345"                        |

4. Configurez la requête **BODY** de façon à utiliser **RAW - JSON (application.json)**  avec la charge utile JSON suivante :

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Sélectionnez le bouton **Code**  qui apparaît dans le coin supérieur droit sous le bouton **Save**. La requête doit ressembler à l’exemple suivant :

   ```xml
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

6. Sélectionnez le bouton **Send**.

Vous devez obtenir un code d’état de réussite **201 Created** et recevoir la notification sur l’appareil client.

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant d’une application Swift iOS de base connectée à un hub de notification par le biais de [l’API REST Notification Hubs](/rest/api/notificationhubs/) et qui peut envoyer et recevoir des notifications. Pour découvrir comment envoyer des notifications à des appareils spécifiques, passez au tutoriel suivant :

- [Tutoriel : Notifications Push vers des appareils spécifiques](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

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
