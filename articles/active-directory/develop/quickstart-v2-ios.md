---
title: Guide de démarrage rapide iOS pour la plateforme d’identités Microsoft | Azure
description: Apprenez à connecter des utilisateurs et à interroger Microsoft Graph dans une application iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: brandwe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3802d8f92913e416cc6a80f899179fde80cec30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962590"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Démarrage rapide : Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ce démarrage rapide contient un exemple de code qui montre comment une application iOS native peut connecter des comptes personnels, professionnels et scolaires, obtenir un jeton d’accès et appeler l’API Microsoft Graph.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Composants requis**
> * XCode 10+
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement vos application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application :
> 1. Accédez au nouveau volet [Portail Azure - Inscriptions des applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Accédez à la page [Inscriptions des applications](https://aka.ms/MobileAppReg) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>      - Dans la section **Nom**, saisissez un nom d'application explicite qui sera présenté aux utilisateurs de votre application lorsqu'ils se connecteront ou accepteront celle-ci, par exemple `iOSQuickstart`.
>      - Ignorez les autres configurations de cette page. 
>      - Appuyez sur le bouton `Register`.
> 1. Cliquez sur la nouvelle application et accédez à `Authentication` > `Add Platform` > `iOS`.    
>      - Entrez l'***identificateur d'offre groupée*** de votre application. 
> 1. Appuyez sur `Configure` et enregistrez les détails de la ***configuration MSAL*** pour une utilisation ultérieure. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Étape 1 : Configuration de votre application
> Pour que l'exemple de code de ce guide de démarrage rapide fonctionne, vous devez ajouter un URI de redirection compatible avec le répartiteur d'authentification. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-ios/green-check.png) Votre application est configurée avec ces attributs

#### <a name="step-2-download-your-web-server-or-project"></a>Étape 2 : Télécharger votre projet ou serveur web

- [Télécharger l'exemple de code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Étape 3 : Configurer votre projet

> [!div renderon="docs"]
> Si vous avez sélectionné l’option 1 ci-dessus, vous pouvez ignorer ces étapes. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrayez le fichier zip et ouvrez le projet dans XCode.
> 1. Modifiez **ViewController.swift** et remplacez la ligne commençant par « let kClientID » avec l’extrait de code suivant :
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Cliquez avec le bouton droit sur **Info.plist** et sélectionnez **Ouvrir en tant que** > **Code source**.
> 1. Remplacez le nœud racine dict par votre ***ID d'offre groupée*** :
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Générez et exécutez l'application ! 

> [!div renderon="docs"]
>
> 1. Extrayez le fichier zip et ouvrez le projet dans XCode.
> 1. Modifiez **ViewController.swift** et remplacez la ligne commençant par « let kClientID » par l'extrait de code suivant :
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Cliquez avec le bouton droit sur **Info.plist** et sélectionnez **Ouvrir en tant que** > **Code source**.
> 1. Remplacez le nœud racine dict par votre ***ID d'offre groupée*** :
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Générez et exécutez l'application ! 

## <a name="more-information"></a>Informations complémentaires

Parcourez ces sections pour en savoir plus sur ce démarrage rapide.

### <a name="getting-msal"></a>Obtention de MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d'accéder à une API protégée par la plateforme d'identités Microsoft. Vous pouvez ajouter MSAL à votre application en suivant le processus ci-après :

```
$ vi Podfile

```
Ajoutez ce qui suit à ce podfile (avec la cible de votre projet) :

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```swift
import MSAL
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

```

> |Où : ||
> |---------|---------|
> | `clientId` | L’ID d’application de l’application inscrite dans *portal.azure.com* |
> | `authority` | Point de terminaison de la plateforme d’identités Microsoft. Dans la plupart des cas, il s’agit de *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | URI de redirection de l'application. Vous pouvez passer « nil » pour utiliser la valeur par défaut, ou votre URI de redirection personnalisé. |

### <a name="additional-app-requirements"></a>Exigences supplémentaires pour les applications  

Votre application doit également comporter ce qui suit dans la propriété `AppDelegate`. Cela permet au kit de développement logiciel (SDK) MSAL de gérer la réponse des jetons de l'application Répartiteur d'authentification au moment de l'authentification.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Enfin, votre application doit comporter une entrée `LSApplicationQueriesSchemes` dans la liste ***Info.plist*** en plus des `CFBundleURLTypes`. Ceci est inclus dans l'exemple fourni. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Connexion des utilisateurs et demandes de jetons

MSAL utilise deux méthodes pour acquérir des jetons : `acquireToken` et `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken : Obtenir un jeton de manière interactive

Certaines situations nécessitent l'interaction des utilisateurs avec la Plateforme d'identités Microsoft. Dans ce cas, l'utilisateur final peut être amené à sélectionner son compte, à saisir ses informations d'identification ou à accepter les autorisations relatives à votre application. Par exemple, 

* La première connexion des utilisateurs à l’application
* Si un utilisateur réinitialise son mot de passe, il doit saisir ses informations d'identification 
* Lorsque votre application demande l'accès à une ressource pour la première fois
* Lorsque l'authentification multifacteur ou d'autres stratégies d'accès conditionnel sont requises

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Où :||
> |---------|---------|
> | `scopes` | Contient les étendues demandées (c'est-à-dire `[ "user.read" ]` pour Microsoft Graph ou `[ "<Application ID URL>/scope" ]` pour les API web personnalisées (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent : Obtention d’un jeton d’accès en mode silencieux

Les applications ne doivent pas demander aux utilisateurs de se connecter chaque fois qu'elles ont besoin d'un jeton. Si l'utilisateur est déjà connecté, cette méthode permet aux applications demander des jetons en mode silencieux. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Où : ||
> |---------|---------|
> | `scopes` | Contient les étendues demandées (c'est-à-dire `[ "user.read" ]` pour Microsoft Graph ou `[ "<Application ID URL>/scope" ]` pour les API web personnalisées (`api://<Application ID>/access_as_user`) |
> | `account` | Compte pour lequel un jeton est demandé. Ce guide de démarrage rapide est une application à compte unique. Si vous souhaitez créer une application à plusieurs comptes, vous devez définir une logique permettant d'identifier le compte à utiliser pour les demandes de jetons `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Étapes suivantes

Essayez le didacticiel iOS pour bénéficier d'un guide pas à pas complet sur la création d'applications ainsi que d'une description détaillée de ce guide de démarrage rapide.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Découvrez les étapes permettant de créer l’application utilisée dans ce démarrage rapide

> [!div class="nextstepaction"]
> [Didacticiel iOS pour appeler l’API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]