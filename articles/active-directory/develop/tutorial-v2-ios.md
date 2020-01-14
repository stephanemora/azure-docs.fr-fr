---
title: Tutoriel MSAL pour iOS et macOS – Plateforme d’identités Microsoft | Azure
description: Découvrez comment les applications iOS et macOS (Swift) peuvent appeler une API qui nécessite des jetons d’accès en utilisant la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0aa97f2cb54295d9403ec332eb9c0ada684df12
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423401"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application iOS ou macOS

Dans ce tutoriel, vous allez apprendre à intégrer une application iOS ou macOS avec la plateforme d’identités Microsoft. L’application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph.  

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Fonctionnement de ce tutoriel

![Fonctionnement de l’exemple d’application généré par ce tutoriel](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

L’application utilisée dans ce tutoriel est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers.  Ces données sont accessibles par le biais d’une API protégée (l’API Microsoft Graph dans ce cas précis) qui nécessite une autorisation et est protégée par la plateforme d’identités Microsoft.

Plus précisément :

* Votre application connectera l’utilisateur via un navigateur ou Microsoft Authenticator.
* L’utilisateur final acceptera les autorisations que votre application a demandées.
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft (MSAL) afin d’implémenter l’authentification. MSAL renouvelle automatiquement les jetons, fournit une authentification unique (SSO) entre les autres applications de l’appareil et gère les comptes.

Ce tutoriel est applicable aux applications iOS et macOS. Notez que certaines étapes varient d’une plateforme à l’autre. 

## <a name="prerequisites"></a>Conditions préalables requises

- XCode version 10.x ou supérieure est nécessaire pour générer l’application de ce guide. Vous pouvez télécharger XCode sur le [site web iTunes ](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de téléchargement de XCode").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Vous pouvez utiliser un gestionnaire de dépendances ou ajouter la bibliothèque manuellement. Les instructions ci-dessous montrent comment procéder.

Ce tutoriel va créer un projet. Si vous voulez plutôt télécharger le tutoriel complet, téléchargez le code :
- [Exemple de code iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Exemple de code macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Création d'un projet

1. Ouvrez Xcode et sélectionnez **Create a new Xcode project** (Créer un projet Xcode).
2. Pour les applications iOS, sélectionnez **iOS** > **Application avec affichage unique**, puis **Suivant**.
3. Pour les applications macOS, sélectionnez **macOS** > **Application Cocoa**, puis **Suivant**.
4. Spécifiez un nom de produit.
5. Choisissez **Swift** comme **Langage**, puis sélectionnez **Suivant**.
6. Sélectionnez un dossier où créer votre application, puis cliquez sur **Créer**.

## <a name="register-your-application"></a>Inscrivez votre application

1. Accédez au [Portail Azure](https://aka.ms/MobileAppReg).
2. Ouvrez le panneau [Inscriptions d’applications](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), puis cliquez sur **+Nouvelle inscription**.
3. Entrez un **Nom** pour votre application, puis, sans définir d’URI de redirection, cliquez sur **Inscrire**.
4. Dans la section **Gérer** du volet qui apparaît, sélectionnez **Authentification**.

5. Cliquez sur **Essayer la nouvelle expérience** en haut de l’écran pour ouvrir la nouvelle expérience d’inscription d’application, puis cliquez sur **+Nouvelle inscription** >  **+Ajouter une plateforme** > **iOS**.
    - Entrez l’ID de bundle de votre projet. Si vous avez téléchargé le code, cette valeur est `com.microsoft.identitysample.MSALiOS`. Si vous créez votre propre projet, sélectionnez-le dans Xcode et ouvrez l’onglet **Général**. L’identificateur de bundle apparaît dans la section **Identité**.
    - Notez que pour macOS, vous devez aussi utiliser l’expérience iOS. 
6. Cliquez sur `Configure` et enregistrez la **Configuration MSAL** qui apparaît dans la page **Configuration iOS** pour pouvoir l’entrer plus tard quand vous configurerez votre application.  Cliquez sur **Done**.

## <a name="add-msal"></a>Ajouter MSAL

Choisissez l’une des méthodes suivantes pour installer la bibliothèque MSAL dans votre application :

### <a name="cocoapods"></a>CocoaPods

1. Si vous utilisez [CocoaPods](https://cocoapods.org/), installez `MSAL` en commençant par créer un fichier vide nommé `podfile` dans le même dossier que le fichier `.xcodeproj` de votre projet. Ajoutez la ligne suivante à `podfile` :

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Remplacez `<your-target-here>` par le nom de votre projet.
3. Dans une fenêtre de terminal, accédez au dossier qui contient le `podfile` que vous avez créé et exécutez `pod install` pour installer la bibliothèque MSAL.
4. Fermez Xcode et ouvrez `<your project name>.xcworkspace` pour recharger le projet dans Xcode.

### <a name="carthage"></a>Carthage

Si vous utilisez [Carthage](https://github.com/Carthage/Carthage), installez `MSAL` en l’ajoutant à votre `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

À partir d’une fenêtre de terminal, dans le même répertoire que le `Cartfile` mis à jour, exécutez la commande suivante pour que Carthage mette à jour les dépendances dans votre projet.

iOS :

```bash
carthage update --platform iOS
```

macOS :

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuellement

Vous pouvez également utiliser Git Submodule ou consulter la dernière version pour l’utiliser comme framework dans votre application.

## <a name="add-your-app-registration"></a>Ajouter votre inscription d’application

Ensuite, nous allons ajouter votre inscription d’application à votre code. 

Tout d’abord, ajoutez l’instruction d’importation suivante au début des fichiers `ViewController.swift` et `AppDelegate.swift` :

```swift
import MSAL
```

Ajoutez ensuite le code suivant à `ViewController.swift` avant `viewDidLoad()` :

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

La seule valeur que vous devez modifier ci-dessus est la valeur attribuée à `kClientID` comme [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Cette valeur fait partie des données de configuration MSAL que vous avez enregistrées au début de ce tutoriel pour inscrire l’application dans le portail Azure.

## <a name="for-ios-only-configure-url-schemes"></a>Pour iOS uniquement, configurer des schémas d’URL

Lors de cette étape, vous allez inscrire `CFBundleURLSchemes` afin que l’utilisateur puisse être redirigé vers l’application après la connexion. Notez que `LSApplicationQueriesSchemes` permet également à votre application d’utiliser Microsoft Authenticator.

Dans Xcode, ouvrez `Info.plist` en tant que fichier de code source et ajoutez ce qui suit dans la section `<dict>`. Remplacez `[BUNDLE_ID]` par la valeur que vous avez utilisée dans le portail Azure (si vous avez téléchargé le code, il s’agit de `com.microsoft.identitysample.MSALiOS`). Si vous créez votre propre projet, sélectionnez-le dans Xcode et ouvrez l’onglet **Général**. L’identificateur de bundle apparaît dans la section **Identité**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Pour macOS uniquement, configurer App Sandbox

1. Accédez à vos paramètres de projet Xcode > **Onglet Capacités** > **App Sandbox**.
2. Cochez la case **Connexions sortantes (client)** . 

## <a name="create-your-apps-ui"></a>Créer l’interface utilisateur de votre application

À présent, créez une interface utilisateur qui comprend un bouton pour appeler l’API Microsoft Graph, un autre pour se déconnecter, et une vue texte pour afficher une sortie en ajoutant le code suivant à la classe `ViewController` :

### <a name="ios-ui"></a>Interface utilisateur iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>Interface utilisateur macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Ensuite, à l’intérieur de la classe `ViewController`, remplacez la méthode `viewDidLoad()` par :

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Utiliser MSAL

### <a name="initialize-msal"></a>Initialiser MSAL

Ajoutez la méthode `initMSAL` suivante à la classe `ViewController` :

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Ajoutez ce qui suit après la méthode `ViewController` dans la classe `initMSAL`.

### <a name="ios-code"></a>Code iOS :

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>Code macOS :

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Pour iOS uniquement, gérer le rappel de connexion

Ouvrez le fichier `AppDelegate.swift` . Pour gérer le rappel après la connexion, ajoutez `MSALPublicClientApplication.handleMSALResponse` à la classe `appDelegate` comme suit :

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Si vous utilisez Xcode 11**, vous devez placer le rappel MSAL dans `SceneDelegate.swift`.
Si vous prenez en charge à la fois UISceneDelegate et UIApplicationDelegate pour assurer la compatibilité avec une version plus ancienne d’iOS, le rappel MSAL doit être placé dans les deux fichiers.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Acquérir des jetons

Nous pouvons maintenant implémenter la logique de traitement de l’interface utilisateur de l’application et obtenir des jetons de manière interactive par le biais de MSAL.

MSAL expose deux méthodes principales pour obtenir des jetons : `acquireTokenSilently()` et `acquireTokenInteractively()` : 

- `acquireTokenSilently()` tente de connecter un utilisateur et d’obtenir des jetons sans aucune intervention de l’utilisateur tant qu’un compte est présent.

- `acquireTokenInteractively()` affiche toujours l’interface utilisateur lors de la tentative de connexion de l’utilisateur. Elle peut utiliser des cookies de session dans le navigateur ou un compte dans Microsoft Authenticator pour fournir une expérience d’authentification unique interactive.

Ajoutez le code suivant à la classe `ViewController` :

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Obtenir un jeton de manière interactive

Le code ci-dessous obtient un jeton pour la première fois en créant un objet `MSALInteractiveTokenParameters` et en appelant `acquireToken`. Ensuite, vous allez ajouter du code qui :

1. Crée `MSALInteractiveTokenParameters` avec des étendues.
2. Appelle `acquireToken()` avec les paramètres créés.
3. Gère les erreurs. Pour plus d’informations, consultez le [guide de gestion des erreurs MSAL pour iOS et macOS](msal-handling-exceptions.md).
4. Gère le cas de réussite.

Ajoutez le code suivant à la classe `ViewController` .

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Obtenir un jeton en mode silencieux

Pour obtenir un jeton mis à jour en mode silencieux, ajoutez le code suivant à la classe `ViewController`. Il crée un objet `MSALSilentTokenParameters` et appelle `acquireTokenSilent()` :

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Appeler l’API Microsoft Graph 

Après avoir obtenu un jeton, votre application peut l’utiliser dans l’en-tête HTTP pour envoyer une requête autorisée à Microsoft Graph :

| clé d’en-tête    | value                 |
| ------------- | --------------------- |
| Autorisation | Porteur \<jeton-accès> |

Ajoutez le code suivant à la classe `ViewController` :

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Pour en savoir plus sur l’API Microsoft Graph, consultez [Microsoft Graph API](https://graph.microsoft.com).

### <a name="use-msal-for-sign-out"></a>Utiliser MSAL pour se déconnecter

Ajoutez ensuite la prise en charge de la déconnexion.

> [!Important]
> La déconnexion de MSAL entraîne la suppression de toutes les informations connues sur un utilisateur dans l’application. Toutefois, l’utilisateur a toujours une session active sur son appareil. Si l’utilisateur tente de se reconnecter, il peut éventuellement voir l’IU de connexion, mais il n’est pas obligé de ressaisir ses informations d’identification, car la session sur l’appareil est toujours active.

Pour ajouter une fonctionnalité de déconnexion, ajoutez le code suivant dans la classe `ViewController`. Cette méthode parcourt tous les comptes et les supprime :

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Activer la mise en cache des jetons

Par défaut, MSAL met en cache les jetons de votre application dans le trousseau iOS ou macOS. 

Pour activer la mise en cache des jetons
1. Vérifiez que votre application est signée correctement.
2. Accédez à vos paramètres de projet Xcode > **onglet Fonctionnalités** > **Activer le partage de trousseau**.
3. Cliquez sur **+** et fournissez l’une des entrées **Groupes de trousseaux** suivantes : 3.a. Pour iOS, entrez `com.microsoft.adalcache` 3.b. Pour macOS, entrez `com.microsoft.identity.universalstorage`.

### <a name="add-helper-methods"></a>Ajouter des méthodes d’assistance
Ajoutez les méthodes d’assistance suivantes à la classe `ViewController` pour compléter l’exemple.

### <a name="ios-ui"></a>Interface utilisateur iOS :

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>Interface utilisateur macOS :

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Applications multicompte

Cette application est adaptée à un scénario de compte unique. MSAL prend également en charge les scénarios multicomptes. Toutefois, cela nécessite du travail supplémentaire de la part des applications. Vous devrez créer l’interface utilisateur pour aider les utilisateurs à sélectionner le compte qu’ils souhaitent utiliser pour chaque action qui nécessite des jetons. Sinon, votre application peut implémenter une approche heuristique pour sélectionner le compte à utiliser via la méthode `getAccounts()`.

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Générez et déployez l’application sur un appareil de test ou un simulateur. Vous devez pouvoir vous connecter et obtenir des jetons pour les comptes Azure AD ou les comptes personnels Microsoft.

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées.  Bien que la plupart des utilisateurs puissent donner leur accord, certains locataires Azure AD ont désactivé le consentement de l’utilisateur, ce qui oblige les administrateurs à donner leur consentement au nom de tous les utilisateurs. Pour permettre la prise en charge de ce scénario, inscrivez les étendues de votre application sur le portail Azure.

Une fois que vous êtes connecté, l’application affiche les données retournées par le point de terminaison `/me` Microsoft Graph.

## <a name="get-help"></a>Obtenir de l’aide

Si vous rencontrez des problèmes avec ce tutoriel ou avec la plateforme d’identités Microsoft, consultez le [Centre d’aide et de support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
