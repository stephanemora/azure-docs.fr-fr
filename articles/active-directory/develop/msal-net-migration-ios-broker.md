---
title: Migration d’applications Xamarin iOS d’ADAL.NET vers MSAL.NET à l’aide de Microsoft Authenticator | Azure
description: Découvrez comment migrer des applications Xamarin iOS de la bibliothèque Azure AD Authentication pour .NET (ADAL.NET) vers la bibliothèque Microsoft Authenticator pour .NET (MSAL.NET) à l’aide de Microsoft Authenticator
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875602"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migration d’applications iOS de ADAL.NET vers MSAL.NET à l’aide de Microsoft Authenticator

Vous avez utilisé ADAL.NET et le répartiteur iOS, et il est temps de migrer vers la [bibliothèque Microsoft Authenticator](msal-overview.md) MSAL.NET qui prend en charge le répartiteur sur iOS depuis la version 4.3. 

Prêt à commencer ? Cet article vous aidera à migrer votre application Xamarin iOS d’ADAL vers MSAL.

## <a name="prerequisites"></a>Prérequis
Ce document part du principe que vous disposez déjà d’une application Xamarin iOS intégrée avec le répartiteur iOS. Si ce n’est pas le cas, il serait préférable de passer directement à MSAL.NET pour commencer à implémenter le répartiteur. Pour plus d’informations sur l’appel du répartiteur iOS dans MSAL.NET avec une nouvelle application, voir [cette documentation](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Arrière-plan

### <a name="what-are-brokers"></a>Que sont les répartiteurs ?

Les répartiteurs sont des applications Microsoft sur Android et iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) sur iOS et Android, portail d’entreprise Intune sur Android). 

Ils permettent d’effectuer les opérations suivantes :

- authentification unique ;
- identification d’appareil requise par certaines [stratégies d’accès conditionnel](../conditional-access/overview.md) (voir [Gestion des appareils](../conditional-access/conditions.md#device-platforms)) ;
- vérification de l’identification d’application, également requise dans certains scénarios d’entreprise (voir, par exemple, [Gestion des applications mobiles ou GAM Intune](https://docs.microsoft.com/intune/mam-faq)).

## <a name="migrate-from-adal-to-msal"></a>Migrer d’ADAL vers MSAL

### <a name="step-1-enable-the-broker"></a>Étape 1 : Activer le répartiteur

<table>
<tr><td>Code ADAL actuel :</td><td>Contrepartie MSAL :</td></tr>
<tr><td>
Dans ADAL.NET, la prise en charge du répartiteur était activée pour chaque contexte d’authentification, et désactivée par défaut. Vous deviez définir un indicateur `PlatformParameters` sur la valeur true dans le constructeur `useBroker` pour appeler le répartiteur :

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Par ailleurs, dans le code spécifique de la plateforme de cet exemple, dans le convertisseur de page pour iOS, vous devez définir l’indicateur `useBroker` 
sur true :
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Ensuite, incluez les paramètres dans l’appel d’acquisition de jeton :
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
Dans MSAL.NET, la prise en charge du répartiteur est activée pour chaque application cliente publique. Elle est désactivés par défaut. Pour l’activer, utilisez : 

le paramètre `WithBroker()` (défini sur true par défaut) pour appeler répartiteur :

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Dans l’appel d’acquisition de jeton :
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Étape 2 : Définir un UIViewController()
Dans ADAL.NET, vous avez passé la valeur UIViewController dans les paramètres PlatformParameters (voir l’exemple de l’étape 1). Or, dans MSAL.NET, pour offrir plus de flexibilité aux développeurs, une fenêtre d’objet est utilisée, mais n’est pas obligatoire dans l’utilisation normale d’iOS. Toutefois, pour pouvoir utiliser le répartiteur, vous devez définir la fenêtre d’objet pour qu’elle envoie et reçoive des réponses du répartiteur. 
<table>
<tr><td>Code ADAL actuel :</td><td>Contrepartie MSAL :</td></tr>
<tr><td>
La valeur UIViewController est transmise aux paramètres PlatformParamters dans la plateforme spécifique d’iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
Dans MSAL.NET, vous devez effectuer deux opérations pour définir la fenêtre d’objet pour iOS :

1) Dans `AppDelegate.cs`, affectez la valeur `App.RootViewController` à un nouveau `UIViewController()`. Cette affectation vérifie l’existence d’un UIViewController avec l’appel au répartiteur. S’il n’est pas défini correctement, vous pouvez recevoir cette erreur : `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Sur l’appel AcquireTokenInteractive, utilisez la valeur `.WithParentActivityOrWindow(App.RootViewController)`
et transmettez la référence à la fenêtre d’objet que vous allez utiliser.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Étape 3 : Mettre à jour AppDelegate pour gérer le rappel
ADAL et MSAL appellent le répartiteur, puis celui-ci rappelle votre application par le biais de la méthode `OpenUrl` de la classe `AppDelegate`. Des informations supplémentaires sont disponibles [ici](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback).

:heavy_check_mark :**aucune différence entre ADAL.NET et MSAL.NET**

### <a name="step-4-register-a-url-scheme"></a>Étape 4 : Inscrire un schéma d’URL
ADAL.NET et MSAL.NET utilisent des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à l’application. Inscrivez le modèle d’URL dans le fichier `Info.plist` de votre application comme suit :

<table>
<tr><td>Code ADAL actuel :</td><td>Contrepartie MSAL :</td></tr>
<tr><td>
Le modèle d’URL est propre à votre application.
</td><td>
Le nom 

`CFBundleURLSchemes` doit inclure 

`msauth.`

en tant que préfixe, suivi de votre `CFBundleURLName`.

Par exemple : `$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
>  Ce modèle d’URL fera partie du RedirectUri utilisé pour identifier de manière unique votre application lors de la réception de la réponse du répartiteur.

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Étape 5 : LSApplicationQueriesSchemes

ADAL.NET et MSAL.NET utilisent `-canOpenURL:` pour vérifier si le répartiteur est installé sur l’appareil. Ajoutez l’identificateur correct pour le répartiteur iOS à la section LSApplicationQueriesSchemes du fichier info.plist comme suit : 
<table>
<tr><td>Code ADAL actuel :</td><td>Contrepartie MSAL :</td></tr>
<tr><td>
Utilisations 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utilisations 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Étape 6 : Inscrivez votre RedirectUri sur le portail

ADAL.NET et MSAL.NET ajoutent tous deux une exigence supplémentaire au redirectUri lors du ciblage du répartiteur. Enregistrez l’URI de redirection avec votre application sur le portail.
<table>
<tr><td>Code ADAL actuel :</td><td>Contrepartie MSAL :</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"` exemple : `mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

exemple :

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Pour plus d’informations sur l’inscription du RedirectUri sur le portail, voir [Utilisation du répartiteur dans des applications Xamarin iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [considérations spécifiques de Xamarin iOS avec MSAL.NET](msal-net-xamarin-ios-considerations.md). 
