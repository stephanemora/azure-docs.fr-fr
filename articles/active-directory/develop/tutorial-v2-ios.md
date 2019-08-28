---
title: Prise en main d’iOS - Plateforme d’identité Microsoft | Azure
description: Comment les applications iOS (Swift) peuvent appeler une API qui nécessite des jetons d’accès pour la plateforme d’identité Microsoft
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/14/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c9afa63a26b7ac990867517f44825054b4c5436
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512398"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application iOS

Dans ce tutoriel, vous allez apprendre à intégrer une application iOS à la plateforme d’identités Microsoft. L’application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph.  

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

## <a name="prerequisites"></a>Prérequis

- XCode version 10. x est nécessaire pour générer l’application dans ce guide. Vous pouvez télécharger XCode sur le [site web d’iTunes ](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Download URL").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Vous pouvez utiliser un gestionnaire de dépendances ou ajouter la bibliothèque manuellement. Les instructions ci-dessous montrent comment procéder.

Ce tutoriel va créer un projet. Si vous souhaitez plutôt télécharger le tutoriel complet, [téléchargez le code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Création d'un projet

1. Ouvrez Xcode et sélectionnez **Create a new Xcode project** (Créer un projet Xcode).
2. Sélectionnez **iOS** > **Application avec affichage unique**, puis **Suivant**.
3. Spécifiez un nom de produit.
4. Choisissez **Swift** comme **Langage**, puis sélectionnez **Suivant**.
5. Sélectionnez un dossier où créer votre application, puis cliquez sur **Créer**.

## <a name="register-your-application"></a>Inscrivez votre application

1. Accédez au [Portail Azure](https://aka.ms/MobileAppReg).
2. Ouvrez le panneau [Inscriptions d’applications](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), puis cliquez sur **+Nouvelle inscription**.
3. Entrez un **Nom** pour votre application, puis, sans définir d’URI de redirection, cliquez sur **Inscrire**.
4. Dans la section **Gérer** du volet qui apparaît, sélectionnez **Authentification**.
5. Cliquez sur **Essayer la nouvelle expérience** en haut de l’écran pour ouvrir la nouvelle expérience d’inscription d’application, puis cliquez sur **+Nouvelle inscription** >  **+Ajouter une plateforme** > **iOS**.
    - Entrez l’ID de bundle de votre projet. Si vous avez téléchargé le code, cette valeur est `com.microsoft.identitysample.MSALiOS`. Si vous créez votre propre projet, sélectionnez-le dans Xcode et ouvrez l’onglet **Général**. L’identificateur de bundle apparaît dans la section **Identité**.
6. Cliquez sur `Configure` et enregistrez la **Configuration MSAL** qui apparaît dans la page **Configuration iOS** pour pouvoir l’entrer plus tard quand vous configurerez votre application.  Cliquez sur **Done**.

## <a name="add-msal"></a>Ajouter MSAL

Choisissez l’une des méthodes suivantes pour installer la bibliothèque MSAL dans votre application :

### <a name="cocoapods"></a>CocoaPods

1. Si vous utilisez [CocoaPods](https://cocoapods.org/), installez `MSAL` en commençant par créer un fichier vide nommé `podfile` dans le même dossier que le fichier `.xcodeproj` de votre projet. Ajoutez la ligne suivante à `podfile` :

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
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

Dans une fenêtre de terminal, dans le même répertoire que le `Cartfile` mis à jour, exécutez la commande suivante pour que Carthage mette à jour les dépendances dans votre projet :

```bash
carthage update --platform iOS
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
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Modifiez la valeur assignée à `kClientID` pour qu’elle corresponde à votre ID d’application. Cette valeur fait partie des données de configuration MSAL que vous avez enregistrées au début de ce tutoriel pour inscrire l’application dans le portail Azure.

## <a name="configure-url-schemes"></a>Configurer les schémas d’URL

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Créer l’interface utilisateur de votre application

À présent, créez une interface utilisateur qui comprend un bouton pour appeler l’API Microsoft Graph, un autre pour se déconnecter, et une vue texte pour afficher une sortie en ajoutant le code suivant à la classe `ViewController` :

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

Ensuite, à l’intérieur de la classe `ViewController`, remplacez la méthode `viewDidLoad()` par :

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Utiliser MSAL

### <a name="initialize-msal"></a>Initialiser MSAL

Ajoutez la méthode `InitMSAL` suivante à la classe `ViewController` :

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>Gérer le rappel de connexion

Ouvrez le fichier `AppDelegate.swift` . Pour gérer le rappel après la connexion, ajoutez `MSALPublicClientApplication.handleMSALResponse` à la classe `appDelegate` comme suit :

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Acquérir des jetons

Nous pouvons maintenant implémenter la logique de traitement de l’interface utilisateur de l’application et obtenir des jetons de manière interactive par le biais de MSAL.

MSAL expose deux méthodes principales pour obtenir des jetons : `acquireTokenSilently()` et `acquireTokenInteractively()` : 

- `acquireTokenSilently()` tente de connecter un utilisateur et d’obtenir des jetons sans aucune intervention de l’utilisateur tant qu’un compte est présent.

- `acquireTokenInteractively()` affiche toujours l’interface utilisateur lors de la tentative de connexion de l’utilisateur. Elle peut utiliser des cookies de session dans le navigateur ou un compte dans Microsoft Authenticator pour fournir une expérience d’authentification unique interactive.

Ajoutez le code suivant à la classe `ViewController` :

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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
3. Gère les erreurs. Pour plus d’informations, reportez-vous au [guide de gestion des erreurs iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Gère le cas de réussite.

Ajoutez le code suivant à la classe `ViewController` .

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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
| Authorization | Porteur \<jeton-accès> |

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
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Activer la mise en cache des jetons

Par défaut, MSAL met en cache les jetons de votre application dans le trousseau iOS. 

Pour activer la mise en cache des jetons
1. Accédez à vos paramètres de projet Xcode > **onglet Fonctionnalités** > **Activer le partage de trousseau**.
2. Cliquez sur **+** et entrez `com.microsoft.adalcache` comme entrée **Groupes de trousseaux**.

### <a name="add-helper-methods"></a>Ajouter des méthodes d’assistance

Ajoutez les méthodes d’assistance suivantes à la classe `ViewController` pour terminer l’exemple :

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

### <a name="multi-account-applications"></a>Applications multicompte

Cette application est adaptée à un scénario de compte unique. MSAL prend également en charge les scénarios multicomptes. Toutefois, cela nécessite du travail supplémentaire de la part des applications. Vous devrez créer l’interface utilisateur pour aider l’utilisateur à sélectionner le compte qu’il souhaite utiliser pour chaque action nécessitant des jetons. Sinon, votre application peut implémenter une approche heuristique pour sélectionner le compte à utiliser via la méthode `getAccounts()`.

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Générez et déployez l’application sur un appareil de test ou un émulateur. Vous devez pouvoir vous connecter et obtenir des jetons pour les comptes Azure AD ou les comptes personnels Microsoft.

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées.  Bien que la plupart des utilisateurs puissent donner leur accord, certains locataires Azure AD ont désactivé le consentement de l’utilisateur, ce qui oblige les administrateurs à donner leur consentement au nom de tous les utilisateurs. Pour permettre la prise en charge de ce scénario, inscrivez les étendues de votre application sur le portail Azure.

Une fois que vous êtes connecté, l’application affiche les données retournées par le point de terminaison `/me` Microsoft Graph.

## <a name="get-help"></a>Obtenir de l’aide

Si vous rencontrez des problèmes avec ce tutoriel ou avec la plateforme d’identités Microsoft, consultez le [Centre d’aide et de support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Aidez-nous à améliorer la plateforme d’identité Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)