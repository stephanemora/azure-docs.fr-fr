---
title: Activer les options d’application mobile iOS Swift avec Azure Active Directory B2C
description: Cet article décrit plusieurs façons d’activer les options d’application mobile iOS Swift avec Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b96e33bedcd4f92b6d75ecea02483e2d22c62ac8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041079"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>Activer les options d’authentification dans une application iOS Swift avec Azure AD B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application iOS Swift. 

Avant de commencer, lisez les articles suivants pour vous familiariser avec les concepts : 
* [Configuration de l’authentification dans un exemple d’application iOS Swift avec Azure AD B2C](configure-authentication-sample-ios-app.md)
* [Activation de l’authentification dans votre propre application iOS Swift avec Azure AD B2C](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et votre ID de locataire dans l’URL d’authentification, procédez comme suit : 

1. Suivez les instructions dans [Activer les domaines personnalisés](custom-domain.md).
1. Mettez à jour le membre de classe `kAuthorityHostName` avec votre domaine personnalisé.
1. Mettez à jour le membre de classe `kTenantName` avec votre [ID de locataire](tenant-management.md#get-your-tenant-id).

Le code Swift suivant montre les paramètres de l’application avant la modification :

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

Le code Swift suivant montre les paramètres de l’application après la modification :

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Recherchez votre objet de configuration de la Bibliothèque d’authentification Microsoft (MSAL), puis ajoutez la méthode `withLoginHint()` avec l’indicateur de connexion.

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `domain_hint` avec le nom de domaine correspondant (par exemple `facebook.com`).
1. Passez la liste des paramètres de requête supplémentaires dans l’attribut `extraQueryParameters` de l’objet de configuration MSAL.

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `ui_locales` avec le code langue correspondant (par exemple `en-us`).
1. Passez la liste des paramètres de requête supplémentaires dans l’attribut `extraQueryParameters` de l’objet de configuration MSAL.

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définir la valeur du paramètre (par exemple `germany-promotion`).
1. Passez la liste des paramètres de requête supplémentaires dans l’attribut `extraQueryParameters` de l’objet de configuration MSAL.

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Dans votre code, générez ou obtenez un jeton d'identification, puis définissez le jeton dans une variable (par exemple, `idToken`). 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre `id_token_hint` avec la variable correspondante qui stocke le jeton d’ID.
1. Passez la liste des paramètres de requête supplémentaires dans l’attribut `extraQueryParameters` de l’objet de configuration MSAL.

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


Le journal MSAL doit être défini le plus tôt possible dans la séquence de lancement de l’application, avant toute demande MSAL. Configurez la [journalisation](../active-directory/develop/msal-logging-ios.md) MSAL dans la méthode `application` *AppDelegate.swift*.

L’extrait de code suivant montre comment configurer la journalisation MSAL :

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>Expérience de vue web incorporée

Des navigateurs web sont nécessaires pour l’authentification interactive. Par défaut, la bibliothèque MSAL utilise la vue web du système. Pendant la connexion, la bibliothèque MSAL affiche la vue web du système iOS avec l’interface utilisateur Azure AD B2C.  

Pour plus d'informations, consultez l'article [Personnaliser les navigateurs et les vues web pour iOS/macOS](../active-directory/develop/customize-webviews.md).

En fonction de vos besoins, vous pouvez utiliser la vue web incorporée. Il existe des différences de comportement au niveau visuel et de l’authentification unique entre la vue web incorporée et la vue web du système dans MSAL.

![Capture d’écran illustrant les différences entre l’expérience de vue web du système et l’expérience de vue web incorporée.](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> Nous vous recommandons d’utiliser la plateforme par défaut, qui correspond généralement au navigateur du système. Le navigateur du système mémorise mieux les utilisateurs déjà connectés. Certains fournisseurs d’identité, tels que Google, ne prennent pas en charge une expérience de vue incorporée.

Pour changer ce comportement, remplacez l’attribut `webviewType` de `MSALWebviewParameters` en le définissant sur `wkWebView`. L’exemple suivant montre comment modifier le type de vue web pour le convertir en vue incorporée : 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, reportez-vous à [MSAL pour les options de configuration iOS Swift](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki).
