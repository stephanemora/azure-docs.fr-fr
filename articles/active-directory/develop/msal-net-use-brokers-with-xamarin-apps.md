---
title: Utiliser le portail d’entreprise Microsoft Authenticator ou Microsoft Intune sur des applications Xamarin iOS et Android | Azure
description: Découvrez comment migrer des applications Xamarin iOS de la bibliothèque Azure AD Authentication pour .NET (ADAL.NET) vers la bibliothèque Microsoft Authenticator pour .NET (MSAL.NET) en utilisant Microsoft Authenticator
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875598"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Utiliser le portail d’entreprise Microsoft Authenticator ou Microsoft Intune sur des applications Xamarin

Sur Android et iOS, des répartiteurs tels que le portail d’entreprise Microsoft Authenticator ou Microsoft Intune (Android uniquement) permettent d’effectuer les opérations suivantes :

- L’authentification unique. Vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- L’identification des appareils. En accédant au certificat de l’appareil qui a été créé sur celui-ci lors de son rattachement à un espace de travail.
- Vérification de l’identification de l’application. Lorsqu’une application appelle le répartiteur, elle transmet son URL de redirection et le répartiteur la vérifie.

Pour activer l’une de ces fonctionnalités, les développeurs d’applications doivent utiliser le paramètre `WithBroker()` lors de l’appel de la méthode `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` a la valeur true par défaut. Pour les applications [iOS](#brokered-authentication-for-ios) ou [Android](#brokered-authentication-for-android), les développeurs doivent également suivre les étapes ci-dessous.

## <a name="brokered-authentication-for-ios"></a>Authentification répartie pour iOS

Suivez les étapes ci-dessous pour permettre à votre application Xamarin.iOS de communiquer avec l’application [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="step-1-enable-broker-support"></a>Étape 1 : Activer la prise en charge du répartiteur
La prise en charge du répartiteur est activée pour chaque application cliente publique (PublicClientApplication). Elle est désactivée par défaut. Utilisez le paramètre `WithBroker()` (dont la valeur est true par défaut) lors de la création de l’application cliente publique (PublicClientApplication) via le générateur d’application cliente publique (PublicClientApplicationBuilder).

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Étape 2 : Mettre à jour AppDelegate pour gérer le rappel
Quand MSAL.NET appelle le répartiteur, ce dernier rappelle votre application via la méthode `OpenUrl` de la classe `AppDelegate`. Étant donné que MSAL attend la réponse du répartiteur, votre application doit coopérer pour rappeler MSAL.NET. Pour permettre cette coopération, mettez à jour le fichier `AppDelegate.cs` pour remplacer la méthode ci-dessous.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {               
         return false;                
    }
    
    return true;     
}           
```

Cette méthode appelée à chaque lancement de l’application permet de traiter la réponse du répartiteur et d’accomplir le processus d’authentification initié par MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Étape 3 : Définir un UIViewController()
Toujours dans `AppDelegate.cs`, vous devez définir une fenêtre d’objet. Normalement, avec Xamarin iOS, vous n’avez pas besoin de définir la fenêtre d’objet. En revanche, vous en avez besoin pour l’envoi au répartiteur et la réception de réponses de celui-ci. 

Pour cela, vous devez effectuer deux opérations. 
1) Dans `AppDelegate.cs`, affectez la valeur `App.RootViewController` à un nouveau `UIViewController()`. Cette affectation vérifie l’existence d’un UIViewController avec l’appel au répartiteur. S’il n’est pas défini correctement, vous pouvez recevoir cette erreur : `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Dans l’appel AcquireTokenInteractive, utilisez `.WithParentActivityOrWindow(App.RootViewController)` et transmettez la référence à la fenêtre d’objet que vous allez utiliser.

**Par exemple :**

Dans `App.cs` :
```CSharp
   public static object RootViewController { get; set; }
```
Dans `AppDelegate.cs` :
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Dans l’appel d’acquisition de jeton :
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-4-register-a-url-scheme"></a>Étape 4 : Inscrire un schéma d’URL
MSAL.NET utilise des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à votre application. Pour terminer l’aller-retour, vous devez inscrire un schéma d’URL pour votre application dans le fichier `Info.plist`.

Le nom `CFBundleURLSchemes` doit inclure le préfixe `msauth.`, suivi de votre `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Par exemple :** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ce nom fera partie du RedirectUri utilisé pour identifier de manière unique votre application lors de la réception de la réponse du répartiteur.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-5-lsapplicationqueriesschemes"></a>Étape 5 : LSApplicationQueriesSchemes
MSAL utilise `–canOpenURL:` pour vérifier si le répartiteur est installé sur l’appareil. Dans iOS 9, Apple a verrouillé la liste des schémas dans lesquels une application peut lancer une requête. 

**Ajoutez** **`msauthv2`** à la section `LSApplicationQueriesSchemes` du fichier `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Étape 6 : Inscrire votre RedirectUri sur le portail d’application
L’utilisation du répartiteur ajoute une exigence à votre redirectUri. Le format du redirectUri _**doit**_ être le suivant :
```CSharp
$"msauth.{BundleId}://auth"
```
**Par exemple :**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Vous remarquerez que le RedirectUri correspond au nom `CFBundleURLSchemes` que vous avez inclus dans le fichier `Info.plist`.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Étape 7 : Vérifier que l’URI de redirection est inscrit avec votre application

Cet URI de redirection doit être inscrit sur le portail d’inscription d’application (https://portal.azure.com) en tant qu’URI de redirection valide pour votre application. 

Le portail offre une nouvelle expérience d’inscription d’application pour vous aider à calculer l’URI de réponse réparti à partir de l’ID d’offre groupée :

1. Dans l’inscription d’application, choisissez **Authentification**, puis sélectionnez **Essayer la nouvelle expérience**.
   ![Essayer la nouvelle expérience d’inscription d’application](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Sélectionnez **Ajouter une plateforme**.
   ![Ajouter une plateforme](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Lorsque la liste des plateformes est prise en charge, sélectionnez **iOS**.
   ![Configurer iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Entrez votre ID d’offre groupée comme demandé, puis appuyez sur **Inscrire**.
   ![Entrer l’ID d’offre groupée](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. L’URI de redirection est calculé pour vous.
   ![Copier URI de redirection](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Authentification répartie pour Android

La prise en charge du répartiteur n’est pas disponible pour Android

## <a name="next-steps"></a>Étapes suivantes

[Considérations relatives à la plateforme Windows universelle avec MSAL.NET](msal-net-uwp-considerations.md)