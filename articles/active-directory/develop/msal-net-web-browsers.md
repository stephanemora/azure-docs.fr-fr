---
title: Web des navigateurs dans la bibliothèque d’authentification Microsoft pour .NET | Azure
description: En savoir plus sur les considérations spécifiques lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350cb3fec4d325d6cf5848733c0bae18d5efacca
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076839"
---
# <a name="using-web-browsers-in-msalnet"></a>À l’aide des navigateurs web dans MSAL.NET
Navigateurs Web sont nécessaires pour l’authentification interactive. Par défaut, MSAL.NET prend en charge la [navigateur web système](#system-web-browser-on-xamarinios-and-xamarinandroid) sur Xamarin.iOS et [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Mais [vous pouvez également activer le navigateur Web intégré](#enable-embedded-webviews) selon vos besoins (UX, nécessaire pour l’authentification-unique (SSO), sécurité) dans [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) et [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) applications. Et vous pouvez même [choisissez dynamiquement](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) le navigateur web à utiliser en fonction de la présence de Chrome ou un navigateur prenant en charge les onglets personnalisés de Chrome dans Android.

## <a name="web-browsers-in-msalnet"></a>Navigateurs Web dans MSAL.NET

Il est important de comprendre que lors de l’acquisition d’un jeton de manière interactive, le contenu de la boîte de dialogue n’est pas fourni par la bibliothèque, mais par le STS (Security Token Service). Le point de terminaison d’authentification envoie du code HTML et le JavaScript qui contrôle l’interaction, qui est restituée dans un navigateur web ou d’un contrôle web. Autoriser le STS gérer l’interaction HTML présente de nombreux avantages :

- Le mot de passe (si un a été tapé) n’est jamais stocké par l’application, ni la bibliothèque d’authentification.
- Permet les redirections à d’autres fournisseurs d’identité (par exemple connexion-in avec un compte professionnel ou scolaire ou d’un compte personnel avec MSAL, ou avec un compte de réseau social avec Azure AD B2C).
- Permet de contrôler l’accès conditionnel, par exemple, en demandant à l’utilisateur plusieurs facteurs d’authentification (MFA) pendant la phase d’authentification (entrer un code confidentiel Windows Hello ou appelée sur leur téléphone ou sur une application d’authentification sur leur téléphone) le STS. Dans les cas où l’obligatoire l’authentification multifacteur n’est pas définie il encore, l’utilisateur peut configurer juste à temps dans la même boîte de dialogue.  L’utilisateur entre son numéro de téléphone mobile et reçoit des instructions pour installer une application de l’authentification et l’analyse d’une balise QR pour ajouter leur compte. Ce serveur piloté par interaction est une excellente expérience !
- Permet à l’utilisateur de modifier leur mot de passe dans cette même boîte de dialogue lorsque le mot de passe a expiré (fournissant des champs supplémentaires pour l’ancien mot de passe et le nouveau mot de passe).
- Permet de personnaliser le locataire ou l’application (images) contrôlée par l’administrateur de locataire Azure AD / propriétaire de l’application.
- Permet aux utilisateurs à donner son consentement pour permettre à l’application d’accéder aux ressources / étendues dans leur nom juste après l’authentification.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Navigateur web du système sur Xamarin.iOS et Xamarin.Android

Par défaut, MSAL.NET prend en charge le navigateur web de système de Xamarin.iOS et Xamarin.Android. Pour héberger l’interaction avec le STS, ADAL.NET utilise uniquement le **embedded** navigateur web. Pour toutes les plateformes qui fournissent l’interface utilisateur (autrement dit, pas .NET Core), une boîte de dialogue est fournie par la bibliothèque de l’incorporation d’un contrôle de navigateur Web. MSAL.NET utilise également une vue web intégré pour les applications de bureau .NET et le carnet d’adresses pour la plateforme UWP. Toutefois, il utilise par défaut le **navigateur web système** pour Xamarin iOS et les applications Xamarin Android. Sur iOS, il choisit même l’affichage web à utiliser en fonction de la version du système d’exploitation (iOS12, IOS 11 et les versions antérieures).

À l’aide du navigateur du système présente un avantage de partage de l’état de l’authentification unique avec d’autres applications et aux applications web sans avoir besoin d’un service broker (portail d’entreprise / authentificateur). Le navigateur du système a été utilisé, par défaut, dans le MSAL.NET pour les Xamarin plateformes iOS et Android de Xamarin, car, sur ces plateformes, le navigateur web de système occupe tout l’écran, et l’expérience utilisateur est préférable. La vue web de système n’est pas distinguer dans une boîte de dialogue. Sur iOS, cependant, l’utilisateur peut devoir donner votre consentement pour le navigateur de rappeler l’application, qui peut s’avérer fastidieux.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP n’utilise pas le System Webview

Pour les applications de bureau, toutefois, lancer un System Webview entraîne une expérience utilisateur été, telles que l’utilisateur voit le navigateur, où ils peuvent avoir déjà d’autres onglets ouverts. Et lorsque l’authentification a eu lieu, les utilisateurs Obtient une page demandant de fermer cette fenêtre. Si l’utilisateur ne faites pas attention, ils peuvent fermer l’ensemble du processus (y compris les autres onglets, qui ne sont pas liées à l’authentification). En tirant parti du navigateur du système sur le bureau nécessiterait également ouvrir les ports locaux et à l’écoute sur ces derniers, ce qui peut nécessiter des autorisations avancées pour l’application. Vous, en tant qu’un développeur, un utilisateur ou un administrateur, peut-être réticent à propos de cette exigence.

## <a name="enable-embedded-webviews"></a>Activer des vues Web incorporées 
Vous pouvez également activer les affichages incorporées dans les applications Xamarin.iOS et Xamarin.Android. À compter de MSAL.NET 2.0.0-preview, MSAL.NET prend également en charge à l’aide de la **embedded** webview option. Pour ADAL.NET, webview incorporé est la seule option prise en charge.

En tant que développeur à l’aide de MSAL.NET ciblant Xamarin, vous pouvez choisir d’utiliser des vues Web incorporées ou navigateurs du système. Il s’agit de votre choix selon les problèmes d’expérience et la sécurité utilisateur que vous souhaitez cibler.

Actuellement, MSAL.NET ne prend pas en charge les répartiteurs iOS et Android. Par conséquent, si vous avez besoin fournir l’authentification unique (SSO), le navigateur système peut-être toujours une meilleure option. Prise en charge des courtiers avec le navigateur web intégré se trouve sur le backlog MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Différences entre webview embedded et le navigateur du système 
Il existe certaines visual différences entre webview embedded et le navigateur du système dans MSAL.NET.

**Interactive signe à l’aide de MSAL.NET à l’aide de l’affichage Web incorporé :**

![Embedded](media/msal-net-web-browsers/embedded-webview.png)

**Interactive se connecter à l’aide de MSAL.NET à l’aide du navigateur du système :**

![Navigateur du système](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Options pour développeurs

En tant que développeur à l’aide de MSAL.NET, vous avez plusieurs options pour l’affichage de la boîte de dialogue interactive à partir de SharePoint Team Services :

- **Navigateur du système.** Le navigateur du système est défini par défaut dans la bibliothèque. Si vous utilisez Android, lire [navigateurs du système](msal-net-system-browser-android-considerations.md) pour plus d’informations sur les navigateurs pris en charge pour l’authentification. Lorsque vous utilisez le navigateur du système dans Android, nous vous recommandons de que l’appareil dispose d’un navigateur qui prend en charge les onglets personnalisés de Chrome.  Sinon, l’authentification peut échouer. 
- **Webview incorporé.** Pour utiliser uniquement incorporé webview dans MSAL.NET, il existe des surcharges de la `UIParent()` constructeur disponible pour iOS et Android.

    iOS :

    ```csharp
    public UIParent(bool useEmbeddedWebview)
    ```

    Android :

    ```csharp
    public UIParent(Activity activity, bool useEmbeddedWebview)
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Choix entre le navigateur web incorporé ou navigateur du système sur Xamarin.iOS

Dans votre application iOS, dans `AppDelegate.cs` vous pouvez utiliser le navigateur du système ou webview incorporé.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(true);

// Use only system browser
App.UIParent = new UIParent();
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Choix entre le navigateur web incorporé ou navigateur de système de Xamarin.Android

Dans votre application Android, dans `MainActivity.cs` vous pouvez décider comment implémenter les options d’affichage Web.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);

// or
// Use only system browser
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Détection de la présence des onglets personnalisés dans Xamarin.Android

Si vous souhaitez utiliser le navigateur web de système pour activer l’authentification unique avec les applications en cours d’exécution dans le navigateur, mais que vous êtes soucieux quant à l’expérience utilisateur pour les appareils Android n’ayant ne pas un navigateur avec prise en charge de l’onglet personnalisé, vous avez la possibilité de décider en appelant le `IsSystemWebViewAvailable()` méthode dans < c 2 > `UIParent` . Cette méthode retourne `true` si le PackageManager détecte des onglets personnalisés et `false` s’ils ne sont pas détectés sur l’appareil.

Selon la valeur retournée par cette méthode et de vos exigences, vous pouvez prendre une décision :

- Vous pouvez retourner un message d’erreur personnalisé à l’utilisateur. Par exemple :  « Veuillez installer Chrome pour continuer avec l’authentification » - OR-
- Vous pouvez revenir à l’option embedded webview et lancer l’interface utilisateur comme une webview incorporé.

Le code ci-dessous montre l’option webview incorporé :

```csharp
bool useSystemBrowser = UIParent.IsSystemWebviewAvailable();
if (useSystemBrowser)
{
    // A browser with custom tabs is present on device, use system browser
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
}
else
{
    // A browser with custom tabs is not present on device, use embedded webview
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);
}

// Alternative:
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, !useSystemBrowser);

```

## <a name="net-core-does-not-support-interactive-authentication"></a>.NET core ne prend pas en charge l’authentification interactive

Pour .NET Core, acquisition de jetons de manière interactive n’est pas disponible. En effet, .NET Core ne fournit pas encore l’interface utilisateur. Si vous souhaitez fournir une connexion interactive pour une application .NET Core, vous pouvez laisser l’application présenter à l’utilisateur un code et une URL pour accéder à se connecter manière interactive (voir [le flux de Code appareil](msal-authentication-flows.md#device-code)).