---
title: Activation de l’authentification dans une application iOS Swift avec Azure AD B2C
description: Cet article aborde comment activer l'authentification dans une application iOS Swift à l'aide de blocs de construction Azure Active Directory B2C. Apprenez à utiliser Azure AD B2C pour connecter et inscrire des utilisateurs dans une application iOS Swift.
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
ms.openlocfilehash: bb590ee57cc78c27c0e6cec7dc54cc15a9fd7648
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186098"
---
# <a name="enable-authentication-in-your-own-ios-swift-app-by-using-azure-ad-b2c"></a>Activation de l’authentification dans votre propre application iOS Swift avec Azure AD B2C

Cet article explique comment ajouter l'authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application mobile iOS Swift. Apprenez à intégrer une application iOS Swift à la [bibliothèque d'authentification Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc). 

Utilisez cet article avec l'article [Configuration de l'authentification dans un exemple d'application iOS Swift](./configure-authentication-sample-ios-app.md), en remplaçant l'exemple d'application iOS Swift par votre propre application iOS Swift. Une fois que vous avez suivi les instructions de cet article, votre application acceptera les connexions via Azure AD B2C.

## <a name="prerequisites"></a>Prérequis

Passez en revue les conditions préalables et les instructions d’intégration dans [Configuration de l’authentification dans un exemple d’application iOS Swift avec Azure AD B2C](configure-authentication-sample-ios-app.md).

## <a name="create-an-ios-swift-app-project"></a>Créer un projet d'application iOS Swift

Si vous n’avez pas encore d’application iOS Swift, configurez un nouveau projet en procédant comme suit :

1. Ouvrez [Xcode](https://developer.apple.com/xcode/), puis sélectionnez **Fichier** > **Nouveau** > **Projet**.
1. Pour les applications iOS, sélectionnez **iOS** > **Application**, puis sélectionnez **Suivant**.
1. Sous **Choisissez les options de votre nouveau projet**, fournissez les informations suivantes :
    1. **Nom du produit**, par exemple `MSALiOS`.
    1. **Identificateur de l'organisation**, par exemple `contoso.com`.
    1. Dans le champ **Interface**, sélectionnez **Storyboard**.
    1. Dans le champ **Cycle de vie**, sélectionnez **UIKit App Delegate**.
    1. Dans le champ **Langage**, sélectionnez **Swift**. 
1. Sélectionnez **Suivant**.
1. Sélectionnez un dossier dans lequel créer votre application, puis sélectionnez **Créer**.


## <a name="step-1-install-the-msal-library"></a>Étape 1 : installer la bibliothèque MSAL

1. Utilisez [CocoaPods](https://cocoapods.org/) pour installer la bibliothèque MSAL.   Dans le dossier où se trouve le fichier *.xcodeproj* de votre projet, si le fichier *podfile* n'existe pas, créez un fichier vide appelé *podfile*. Ajoutez le code suivant au fichier *podfile* :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. Remplacez `<your-target-here>` par le nom de votre projet (par exemple, `MSALiOS`). Pour plus d'informations, consultez [Informations de référence sur la syntaxe Podfile](https://guides.cocoapods.org/syntax/podfile.html#podfile).
1. Dans une fenêtre de terminal, accédez au dossier qui contient le fichier *podfile*, puis exécutez *pod install* pour installer la bibliothèque MSAL.
1. Une fois la commande `pod install` exécutée, un fichier *\<your project name>.xcworkspace* est créé. Pour recharger le projet dans Xcode, fermez Xcode, puis ouvrez le fichier *\<your project name>.xcworkspace*.

## <a name="step-2-set-the-app-url-scheme"></a>Étape 2 : définir le schéma d’URL de l’application

Lorsqu'un utilisateur s'authentifie, Azure AD B2C envoie un code d'autorisation à l'application à l'aide de l'URI de redirection configuré lors de l'inscription de l'application Azure AD B2C. 

Le format de l'URI de redirection par défaut de MSAL est `msauth.[Your_Bundle_Id]://auth`. Par exemple, `msauth.com.microsoft.identitysample.MSALiOS://auth`, où `msauth.com.microsoft.identitysample.MSALiOS` correspond au modèle d'URL.

Lors de cette étape, inscrivez votre modèle d'URL à l'aide du tableau `CFBundleURLSchemes`. Votre application écoute le modèle d'URL du rappel à partir d'Azure AD B2C. 

Dans Xcode, ouvrez le fichier [*Info.plist*](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) en tant que fichier de code source. Dans la section `<dict>`, ajoutez l’extrait de code XML suivant : 

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

## <a name="step-3-add-the-authentication-code"></a>Étape 3 : ajouter le code d’authentification

L'[exemple de code](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) est constitué d'une classe `UIViewController`. La classe :

- définit la structure d'une interface utilisateur ;
- contient des informations sur votre fournisseur d'identité Azure AD B2C. L'application utilise ces informations pour établir une relation de confiance avec Azure AD B2C ; 
- Contient le code d'authentification qui permet d'authentifier les utilisateurs, d'acquérir les jetons et de les valider.

Choisissez un `UIViewController` où les utilisateurs s'authentifieront.  Dans votre `UIViewController`, fusionnez le code avec le [code fourni dans GitHub](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift). 

## <a name="step-4-configure-your-ios-swift-app"></a>Étape 4 : Configurer votre application iOS Swift

Après avoir[ajouté le code d'authentification](#step-3-add-the-authentication-code), configurez votre application iOS Swift avec vos paramètres Azure AD B2C. Les paramètres du fournisseur d'identité Azure AD B2C sont configurés dans la classe `UIViewController` choisie à la section précédente. 

Pour savoir comment configurer votre application iOS SWIFT, consultez [Configurer l’authentification dans un exemple d’application iOS Swift à l’aide de Azure AD B2C](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

## <a name="step-5-run-and-test-the-mobile-app"></a>Étape 5 : Exécuter et tester l’application mobile

1. Générez et exécutez le projet avec un [simulateur d'appareil iOS connecté](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device).
1. Sélectionnez **Se connecter**, puis inscrivez-vous ou connectez-vous avec votre compte Azure AD B2C local ou avec un compte social.
1. Une fois que vous vous êtes correctement authentifié, vous verrez votre nom d’affichage dans la barre de navigation.

## <a name="step-6-customize-your-code-building-blocks"></a>Étape 6 : Personnaliser vos blocs de construction de code

Cette section décrit les blocs de construction de code qui permettent l'authentification de votre application iOS Swift. Elle répertorie les méthodes de UIViewController et explique comment personnaliser votre code. 

### <a name="step-61-instantiate-a-public-client-application"></a>Étape 6.1 : Instancier une application cliente publique

Les applications clientes publiques ne sont pas approuvées pour conserver en toute sécurité les secrets d’application. Elles ne disposent pas de clé secrète client. Dans [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload), instanciez une MSAL à l'aide d'un objet d'application cliente publique.

L'extrait de code Swift suivant montre comment initialiser la bibliothèque MSAL avec un objet de configuration `MSALPublicClientApplicationConfig`. 

L’objet de configuration fournit des informations sur votre environnement de Azure AD B2C. Par exemple, il fournit l’ID client, l’URI de redirection et l’autorité pour créer des demandes d’authentification à Azure AD B2C. Pour plus d'informations sur l'objet de configuration, consultez [Configurer l'exemple d'application mobile](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

```swift
do {

    let signinPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: signinPolicyAuthority)
    pcaConfig.knownAuthorities = [signinPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

La méthode `initWebViewParams` configure l'expérience d'[authentification interactive](../active-directory/develop/customize-webviews.md). 

L'extrait de code Swift suivant initialise le membre de la classe `webViewParameters` avec l’affichage web du système. Pour plus d'informations, consultez [Personnaliser les navigateurs et WebViews pour iOS/macOS](../active-directory/develop/customize-webviews.md).

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParameters?.webviewType = .default
}
```

### <a name="step-62-start-an-interactive-authorization-request"></a>Étape 6.2 : Démarrer une requête d’autorisation interactive

Une requête d’autorisation interactive est un flux dans lequel les utilisateurs sont invités à s’inscrire ou à se connecter à l’aide de l’affichage web du système. Lorsque l'utilisateur clique sur le bouton **Se connecter**, la méthode `authorizationButton` est appelée.

La méthode `authorizationButton` prépare l'objet `MSALInteractiveTokenParameters` avec les données pertinentes sur la requête d'autorisation. La méthode `acquireToken` utilise le `MSALInteractiveTokenParameters` pour authentifier l'utilisateur à l'aide de l’affichage web du système.

L’extrait de code suivant montre comment démarrer la requête d’autorisation interactive : 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error information" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
Une fois que les utilisateurs ont terminé le flux d’autorisation (avec succès ou non), le résultat est retourné à la [fermeture](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) de la méthode `acquireToken`. 

La méthode `acquireToken` renvoie les objets `result` et `error`. Utilisez cette clôture pour :

- Mettre à jour l'interface utilisateur de l'application mobile avec les informations une fois l'authentification effectuée
- Appeler un service d'API web avec un jeton d'accès
- Gérer les erreurs d'authentification, (par exemple, lorsqu'un utilisateur annule le flux de connexion).
 
### <a name="step-63-call-a-web-api"></a>Étape 6.3 : Appeler une API Web

Pour appeler une [API web d'autorisation par jeton](enable-authentication-web-api.md), l'application a besoin d'un jeton d'accès valide. L’application effectue les opérations suivantes :

1. Elle obtient un jeton d'accès avec les autorisations (étendues) requises pour le point de terminaison de l'API web.
1. Elle transmet le jeton d’accès en tant que porteur dans l’en-tête d’autorisation de la requête HTTP en utilisant le format suivant :

```http
Authorization: Bearer <access-token>
```

Lorsque les utilisateurs [s'authentifient de manière interactive](#step-62-start-an-interactive-authorization-request), l'application obtient un jeton d'accès dans la clôture `acquireToken`. Pour les appels d'API web suivants, utilisez la méthode d'obtention de jeton sans assistance (`acquireTokenSilent`), comme décrit dans cette section. 

La méthode `acquireTokenSilent` effectue les opérations suivantes :

1. Elle essaie de récupérer (fetch) un jeton d'accès avec les étendues demandées dans le cache des jetons. Si le jeton est présent et qu'il n'a pas expiré, il est renvoyé. 
1. Si le jeton n'est pas présent dans le cache des jetons ou s'il a expiré, la bibliothèque MSAL tente d'utiliser le jeton d'actualisation pour acquérir un nouveau jeton d'accès. 
1. Si le jeton d'actualisation n'existe pas ou s'il a expiré, une exception est renvoyée. Dans ce cas, vous devez inviter l'utilisateur à [se connecter de manière interactive](#step-62-start-an-interactive-authorization-request).  

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

La méthode `callGraphAPI` récupère le jeton d'accès et appelle l'API web, comme montré ici :

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
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error information" as! Error)")
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

### <a name="step-64-sign-out-users"></a>Étape 6.4 : Déconnecter les utilisateurs

La déconnexion avec MSAL entraîne la suppression de toutes les informations connues sur un utilisateur dans l'application. Utilisez la méthode de déconnexion pour déconnecter les utilisateurs et mettre à jour l'interface utilisateur. Par exemple, vous pouvez masquer les éléments protégés de l'interface utilisateur et le bouton de déconnexion, ou afficher le bouton de connexion.

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

Découvrez comment :
* [Configurer les options d’authentification dans une application iOS Swift avec Azure AD B2C](enable-authentication-ios-app-options.md)
* [Activer l’authentification dans votre propre API web avec Azure AD B2C](enable-authentication-web-api.md)
