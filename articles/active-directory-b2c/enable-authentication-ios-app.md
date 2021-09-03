---
title: Activer l'authentification dans une application iOS Swift - Azure AD B2C
description: Activez l'authentification dans une application iOS Swift à l'aide de blocs de construction Azure Active Directory B2C. Apprenez à utiliser Azure AD B2C pour connecter et inscrire des utilisateurs dans une application iOS Swift.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d9efe9b3d7810eccb94906c236b34534861db092
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524051"
---
# <a name="enable-authentication-in-your-own-ios-swift-application-using-azure-active-directory-b2c"></a>Activer l'authentification dans votre propre application iOS Swift à l'aide d'Azure Active Directory B2C

Cet article explique comment ajouter l'authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application mobile iOS Swift. Apprenez à intégrer une application iOS Swift à la bibliothèque d'authentification [MSAL pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc). 

Utilisez cet article avec l'article [Configuration de l'authentification dans un exemple d'application iOS Swift](./configure-authentication-sample-ios-app.md), en remplaçant l'exemple d'application iOS Swift par votre propre application iOS Swift. Une fois que vous aurez suivi les étapes décrites dans cet article, votre application acceptera les connexions via Azure AD B2C.

## <a name="prerequisites"></a>Configuration requise

Passez en revue les conditions préalables et les étapes d'intégration de l'article [Configurer l'authentification dans un exemple d'application iOS Swift](configure-authentication-sample-ios-app.md).

## <a name="create-an-ios-swift-app-project"></a>Créer un projet d'application iOS Swift

Si vous n'avez pas encore d'application iOS Swift, procédez comme suit pour configurer un nouveau projet.

1. Ouvrez [Xcode](https://developer.apple.com/xcode/) et sélectionnez **Fichier** > **Nouveau** > **Projet**.
1. Pour les applications iOS, sélectionnez **iOS** > **Application**, puis **Suivant**.
1. Sous **Choisissez les options de votre nouveau projet**, fournissez les informations suivantes :
    1. **Nom du produit**, par exemple `MSALiOS`.
    1. **Identificateur de l'organisation**, par exemple `contoso.com`.
    1. Dans le champ **Interface**, sélectionnez **Storyboard**.
    1. Dans le champ **Cycle de vie**, sélectionnez **UIKit App Delegate**.
    1. Dans le champ **Langage**, sélectionnez **Swift**. 
1. Sélectionnez **Suivant**.
1. Sélectionnez un dossier où créer votre application, puis sélectionnez **Créer**.


## <a name="install-the-msal-library"></a>Installer la bibliothèque MSAL

1. Utilisez [CocoaPods](https://cocoapods.org/) pour installer la bibliothèque MSAL.   Dans le dossier où se trouve le fichier `.xcodeproj` de votre projet, si le fichier `podfile` n'existe pas, créez un fichier vide appelé `podfile`. Ajoutez le code suivant au fichier `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. Remplacez `<your-target-here>` par le nom de votre projet. Par exemple : `MSALiOS`. Pour plus d'informations, consultez [Informations de référence sur la syntaxe Podfile](https://guides.cocoapods.org/syntax/podfile.html#podfile).
1. Dans une fenêtre de terminal, accédez au dossier qui contient le fichier `podfile`. Exécutez `pod install` pour installer la bibliothèque MSAL.
1. Une fois la commande `pod install` exécutée, un fichier `<your project name>.xcworkspace` est créé. Pour recharger le projet dans Xcode, fermez Xcode et ouvrez `<your project name>.xcworkspace`.

## <a name="set-the-app-url-scheme"></a>Définir le modèle d'URL de l'application

Lorsqu'un utilisateur s'authentifie, Azure AD B2C envoie un code d'autorisation à l'application à l'aide de l'URI de redirection configuré lors de l'inscription de l'application Azure AD B2C. 

Le format de l'URI de redirection par défaut de MSAL est `msauth.[Your_Bundle_Id]://auth`. Par exemple, `msauth.com.microsoft.identitysample.MSALiOS://auth`, où `msauth.com.microsoft.identitysample.MSALiOS` correspond au modèle d'URL.

Lors de cette étape, inscrivez votre modèle d'URL à l'aide du tableau `CFBundleURLSchemes`. Votre application écoute le modèle d'URL du rappel à partir d'Azure AD B2C. 

Dans Xcode, ouvrez le [fichier Info.plist](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) en tant que fichier de code source. Ajoutez l'extrait de code XML suivant dans la section `<dict>`. 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="add-the-authentication-code"></a>Ajouter le code d'authentification

L'[exemple de code](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) est constitué d'une classe `UIViewController`. La classe :

- définit la structure d'une interface utilisateur ;
- contient des informations sur votre fournisseur d'identité Azure AD B2C. L'application utilise ces informations pour établir une relation de confiance avec Azure AD B2C ; 
- contient le code d'authentification qui permet d'authentifier les utilisateurs, d'acquérir les jetons et de les valider.

Choisissez un `UIViewController` où les utilisateurs s'authentifieront. Fusionnez le code avec celui [fourni ici](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift) dans votre `UIViewController`. 

## <a name="configure-your-ios-swift-app"></a>Configurer votre application iOS Swift

Après avoir[ajouté le code d'authentification](#add-the-authentication-code), configurez votre application iOS Swift avec vos paramètres Azure AD B2C. Les paramètres du fournisseur d'identité Azure AD B2C sont configurés dans la classe `UIViewController` choisie à la section précédente. 

Suivez les instructions expliquant comment [Configurer l'exemple d'application mobile](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

## <a name="run-and-test-the-mobile-app"></a>Exécuter et tester l'application mobile

1. Générez et exécutez le projet avec un [simulateur d'appareil iOS connecté](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device).
1. Sélectionnez **Connexion**. Puis inscrivez-vous ou connectez-vous avec votre compte Azure AD B2C local ou social.
1. Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation.

## <a name="how-it-works"></a>Fonctionnement

Cette section décrit les blocs de construction de code qui permettent l'authentification de votre application iOS Swift. Elle répertorie les méthodes de la classe UIViewController et explique comment personnaliser votre code. 

### <a name="instantiate-a-public-client-application"></a>Instancier une application cliente publique

Les applications clientes publiques ne sont pas approuvées pour conserver en toute sécurité les secrets d'application, et elles ne disposent pas de clé secrète client. Dans [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload), instanciez MSAL à l'aide d'un objet d'application cliente publique.

L'extrait de code suivant montre comment initialiser la bibliothèque MSAL avec un objet de configuration `MSALPublicClientApplicationConfig`. 

L'objet de configuration fournit des informations sur votre environnement Azure AD B2C, par exemple, l'ID client, l'URI de redirection et l'autorité permettant de créer des demandes d'authentification auprès d'Azure AD B2C. Pour plus d'informations sur l'objet de configuration, consultez [Configurer l'exemple d'application mobile](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

```swift
do {

    let siginPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: siginPolicyAuthority)
    pcaConfig.knownAuthorities = [siginPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

La méthode `initWebViewParams` configure l'expérience d'[authentification interactive](../active-directory/develop/customize-webviews.md). 

L'extrait de code Swift suivant initialise le membre de la classe `webViewParamaters` avec la vue web du système. Pour plus d'informations, consultez l'article [Personnaliser les navigateurs et les vues web pour iOS/macOS](../active-directory/develop/customize-webviews.md).

```swift
func initWebViewParams() {
    self.webViewParamaters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParamaters?.webviewType = .default
}
```

### <a name="interactive-authorization-request"></a>Requête d'autorisation interactive

Une requête d'autorisation interactive est un flux dans lequel l'utilisateur est invité à s'inscrire ou à se connecter à l'aide d'une vue web du système. Lorsque l'utilisateur clique sur le bouton **Se connecter**, la méthode `authorizationButton` est appelée.

La méthode `authorizationButton` prépare l'objet `MSALInteractiveTokenParameters` avec les données pertinentes sur la requête d'autorisation. La méthode `acquireToken` utilise `MSALInteractiveTokenParameters` pour authentifier l'utilisateur à l'aide de la vue web du système.

L'extrait de code suivant montre comment démarrer la requête d'autorisation interactive. 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error informarion" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
Une fois que l'utilisateur arrive au terme du flux d'autorisation (avec succès ou non), le résultat est renvoyé à la [clôture](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) de la méthode `acquireToken`. 

La méthode acquireToken renvoie les objets `result` et `error`. Utilisez cette clôture pour :

- Mettre à jour l'interface utilisateur de l'application mobile avec les informations une fois l'authentification effectuée
- Appeler un service d'API web avec un jeton d'accès
- Gérer les erreurs d'authentification, par exemple, lorsqu'un utilisateur annule le processus de connexion
 
### <a name="call-a-web-api"></a>Appeler une API web

Pour appeler une [API web d'autorisation par jeton](enable-authentication-web-api.md), l'application a besoin d'un jeton d'accès valide. L'application suit les étapes ci-dessous :

1. Elle obtient un jeton d'accès avec les autorisations (étendues) requises pour le point de terminaison de l'API web.
1. Elle transmet le jeton d'accès en tant que porteur dans l'en-tête d'autorisation de la requête HTTP en utilisant le format suivant :

```http
Authorization: Bearer <access-token>
```

Lorsque les utilisateurs [s'authentifient de manière interactive](#interactive-authorization-request), l'application obtient un jeton d'accès dans la clôture `acquireToken`. Pour les appels d'API web suivants, utilisez la méthode d'obtention de jeton sans assistance (`acquireTokenSilent`), comme décrit dans cette section. 

La méthode `acquireTokenSilent` suit les étapes ci-dessous :

1. Elle essaie de récupérer (fetch) un jeton d'accès avec les étendues demandées dans le cache des jetons. Si le jeton est présent et qu'il n'a pas expiré, il est renvoyé. 
1. Si le jeton n'est pas présent dans le cache des jetons ou s'il a expiré, la bibliothèque MSAL tente d'utiliser le jeton d'actualisation pour acquérir un nouveau jeton d'accès. 
1. Si le jeton d'actualisation n'existe pas ou s'il a expiré, une exception est renvoyée. Dans ce cas, vous devez inviter l'utilisateur à [se connecter de manière interactive](#interactive-authorization-request).  

L'extrait de code suivant montre comment obtenir un jeton d'accès :

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

La méthode `callGraphAPI` récupère le jeton d'accès et appelle l'API web.

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error informarion" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="sign-out"></a>Se déconnecter

La déconnexion de MSAL entraîne la suppression de toutes les informations connues sur un utilisateur dans l'application. Utilisez la méthode de déconnexion pour déconnecter les utilisateurs et mettre à jour l'interface utilisateur. Par exemple, masquez les éléments protégés de l'interface utilisateur et le bouton de déconnexion, et affichez le bouton de connexion.

L'extrait de code suivant montre comment déconnecter un utilisateur :

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Configurer les options d'authentification dans une application iOS Swift](enable-authentication-ios-app-options.md)
* [Activer l'authentification dans votre propre API web](enable-authentication-web-api.md)
