---
title: Prise en main d’iOS - Plateforme d’identité Microsoft | Azure
description: Comment les applications iOS (Swift) peuvent appeler une API qui nécessite des jetons d’accès pour la plateforme d’identité Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550576"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application iOS

Dans ce didacticiel, vous allez apprendre à créer une application iOS et à l’intégrer à la plateforme d’identité Microsoft. Plus précisément, cette application doit connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et adresser une requête de base à l’API Microsoft Graph.  

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

## <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

L’application utilisée dans cet exemple est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers.  Ces données sont accessibles par le biais d’une API protégée (l’API Microsoft Graph dans ce cas précis) qui nécessite une autorisation et est également protégée par la plateforme d’identité Microsoft.

Plus précisément :

* Votre application connectera l’utilisateur via un navigateur ou Microsoft Authenticator.
* L’utilisateur final acceptera les autorisations que votre application a demandées. 
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft (MSAL) afin d’implémenter Auth. MSAL renouvelle automatiquement les jetons, assure l’authentification unique entre les autres applications sur l’appareil, et gère les comptes.

## <a name="prerequisites"></a>Prérequis

- XCode version 10.x est requis pour l’exemple à créer dans ce guide. Vous pouvez télécharger XCode sur le [site web d’iTunes ](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Download URL").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Vous pouvez utiliser le gestionnaire de dépendances ou ajouter les éléments manuellement. La section ci-dessous fournit [plus d’informations](#add-msal). 

## <a name="set-up-your-project"></a>Configuration de votre projet

Ce tutoriel va créer un projet. Si vous souhaitez plutôt télécharger le tutoriel complet, [téléchargez le code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Création d'un projet

1. Ouvrez Xcode et sélectionnez **Create a new Xcode project** (Créer un projet Xcode).
2. Sélectionnez **iOS > Single view Application** (iOS > Application à vue unique) et sélectionnez **Next** (Suivant).
3. Donnez un nom de produit, puis cliquez sur **Next** (Suivant).
4. Sélectionnez un dossier où créer votre application, puis cliquez sur *Créer*.

## <a name="register-your-application"></a>Inscrivez votre application 

Vous pouvez inscrire votre application de deux manières, comme décrit dans les deux sections suivantes.

### <a name="register-your-app"></a>Inscrire votre application

1. Accédez au [portail Azure](https://aka.ms/MobileAppReg) > sélectionnez `New registration`. 
2. Entrez le **nom** de votre application > `Register`. **Ne définissez aucune URI de redirection à ce stade**. 
3. Dans la section `Manage`, accédez à `Authentication` > `Add a platform` > `iOS`
    - Entrez l’ID de bundle de votre projet. Si vous avez téléchargé le code, cette valeur est `com.microsoft.identitysample.MSALiOS`.
4. Appuyez sur `Configure` et stockez la valeur `MSAL Configuration` pour une utilisation ultérieure. 

## <a name="add-msal"></a>Ajouter MSAL

### <a name="get-msal"></a>Obtenir MSAL

#### <a name="cocoapods"></a>CocoaPods

Vous pouvez utiliser [CocoaPods](https://cocoapods.org/) pour installer `MSAL` en l’ajoutant à votre `Podfile`, sous la cible :

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Vous pouvez utiliser [Carthage](https://github.com/Carthage/Carthage) pour installer `MSAL` en l’ajoutant à votre `Cartfile` : 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manuellement

Vous pouvez également utiliser Git Submodule ou consulter la dernière version et l’utiliser comme framework dans votre application.

### <a name="add-your-app-registration"></a>Ajouter votre inscription d’application

Ajoutez ensuite votre inscription d’application à votre code. Ajoutez votre ***ID client/application*** à `ViewController.swift` :

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Configurer les schémas d’URL

Inscrivez `CFBundleURLSchemes` pour autoriser un rappel afin de rediriger l’utilisateur vers l’application après la connexion.

`LSApplicationQueriesSchemes` permet à votre application d’utiliser Microsoft Authenticator, s’il est disponible. 

Pour cela, ouvrez `Info.plist` en tant que code source, puis ajoutez le code suivant, en remplaçant ***BUNDLE_ID*** par la valeur que vous avez configurée dans le portail Azure.

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

### <a name="import-msal"></a>Importer MSAL

Importez le framework MSAL dans les fichiers `ViewController.swift` et `AppDelegate.swift` :

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Créer l’interface utilisateur de votre application

Pour ce tutoriel, vous avez besoin de créer les éléments suivants :

- Bouton pour appeler l’API Graph
- Bouton de déconnexion
- Journalisation Textview

Dans `ViewController.swift`, définissez les propriétés et `initUI()` comme suit :

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

Ajoutez ensuite à `viewDidLoad()` de ViewController.swift :

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

Vous devez d’abord créer un `MSALPublicClientApplication` avec une instance de `MSALPublicClientConfiguration` pour votre application :

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

### <a name="handle-the-callback"></a>Gérer le rappel

Pour gérer le rappel après la connexion, ajoutez `MSALPublicClientApplication.handleMSALResponse` dans `appDelegate` :

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Acquérir des jetons

Nous pouvons maintenant implémenter la logique de traitement de l’interface utilisateur de l’application et obtenir des jetons de manière interactive via MSAL. 

MSAL propose deux méthodes principales pour obtenir des jetons : `acquireTokenSilently` et `acquireTokenInteractively`.  

`acquireTokenSilently()` tente de connecter un utilisateur et obtenir des jetons sans aucune intervention de l’utilisateur si un compte est présent.

`acquireTokenInteractively` affiche toujours l’interface utilisateur lorsque vous tentez de connecter l’utilisateur et d’obtenir des jetons ; toutefois, le processus peut utiliser les cookies de session du navigateur ou un compte dans Microsoft Authenticator pour offrir une expérience interactive d’authentification unique. 

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

Pour acquérir un jeton pour la première fois, vous devez créer un élément `MSALInteractiveTokenParameters` et appelez `acquireToken`.

1. Créez `MSALInteractiveTokenParameters` avec des étendues.
2. Appelez `acquireToken` avec les paramètres créés.
3. Gérez l’erreur en conséquence. Pour plus d’informations, reportez-vous au [guide de gestion des erreurs iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Gérez le cas de réussite. 

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

Pour acquérir un jeton mis à jour en mode silencieux, vous devez créer `MSALSilentTokenParameters` et appeler `acquireTokenSilent` :

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

Après avoir obtenu un jeton via `self.accessToken`, votre application peut utiliser cette valeur dans l’en-tête HTTP pour envoyer une demande autorisée à Microsoft Graph :

| clé d’en-tête    | value                 |
| ------------- | --------------------- |
| Authorization | Porteur \<jeton-accès> |

Ajoutez la ligne suivante à `ViewController.swift` :

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

En savoir plus sur l’[API Graph Microsoft](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Utiliser MSAL pour se déconnecter

Nous allons ensuite ajouter à notre application la prise en charge de la déconnexion. 

Il est important de noter que la déconnexion avec MSAL supprime de cette application toutes les informations connues relatives à un utilisateur, mais ce dernier gardera toujours une session active sur son appareil. Si l’utilisateur tente de se reconnecter, il notera une interaction mais n’aura pas à entrer à nouveau ses identifiants car la session est active sur l’appareil. 

Pour ajouter une option de déconnexion, copiez la méthode suivante dans votre `ViewController.swift` :

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
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

Pour activer la mise en cache des jetons, accédez aux paramètres de votre projet Xcode > `Capabilities tab` > `Enable Keychain Sharing` > cliquez sur `Plus` > Entrée **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Ajouter des méthodes d’assistance

Ajoutez ces méthodes d’assistance pour finaliser l’exemple :

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

Cette application est adaptée à un scénario de compte unique. MSAL prend également en charge les scénarios multicompte, mais nécessite un travail supplémentaire de la part des applications. Vous devrez créer l’interface utilisateur pour aider l’utilisateur à sélectionner le compte qu’il souhaite utiliser pour chaque action nécessitant des jetons. Sinon, votre application peut implémenter une approche heuristique pour sélectionner le compte à utiliser via la méthode `getAllAccounts(...)`.

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Si vous avez suivi le code ci-dessus, essayez de générer et de déployer l’application sur un appareil de test ou un émulateur. Vous devriez pouvoir vous connecter et obtenir des jetons pour Azure AD ou des comptes personnels Microsoft. Une fois l’utilisateur connecté, cette application affichera les données retournées par le point de terminaison Microsoft Graph `/me`. 

Si vous rencontrez des problèmes, n’hésitez pas à ouvrir un problème sur ce document ou dans la bibliothèque MSAL, et faites-nous part de vos commentaires. 

### <a name="consent-to-your-app"></a>Consentement à votre application

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées.  Même si la plupart des utilisateurs peuvent donner leur accord, certains clients Azure AD ont désactivé le consentement de l’utilisateur, obligeant les administrateurs à donnent leur consentement pour le compte de tous les utilisateurs.  Pour prendre en charge ce scénario, veillez à enregistrer les étendues de votre application dans le portail Azure.

## <a name="help-and-support"></a>Aide et support

Avez-vous rencontré des problèmes avec ce tutoriel ou avec la plateforme d’identité Microsoft ? Consultez [Aide et support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

