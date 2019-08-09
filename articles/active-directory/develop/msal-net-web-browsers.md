---
title: Navigateurs web dans la bibliothèque d’authentification pour .NET | Azure
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 061b8a9f16396841c3f0d650ccc2f2c4a907aab3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111306"
---
# <a name="using-web-browsers-in-msalnet"></a>Utilisation de navigateurs web dans MSAL.NET
Des navigateurs web sont nécessaires pour l’authentification interactive. Par défaut, MSAL.NET prend en charge le [navigateur web du système](#system-web-browser-on-xamarinios-and-xamarinandroid) sur Xamarin.iOS et [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Par contre, [vous pouvez aussi activer le navigateur web intégré](#enable-embedded-webviews) en fonction de vos exigences (UX, besoin de l’authentification unique (SSO), sécurité) dans les applications [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) et [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid). Et vous pouvez même [choisir de façon dynamique](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) le navigateur web à utiliser en fonction de la présence de Chrome, ou d’un navigateur prenant en charge les onglets personnalisés de Chrome dans Android.

## <a name="web-browsers-in-msalnet"></a>Navigateurs web dans MSAL.NET

Il est important de comprendre que lors de l’acquisition d’un jeton de manière interactive, le contenu de la boîte de dialogue n’est pas fourni par la bibliothèque, mais par le service d’émission de jeton de sécurité (STS). Le point de terminaison d’authentification retourne du code HTML et JavaScript qui contrôle l’interaction, ce qui est restitué dans un navigateur web ou un contrôle web. Autoriser le STS pour gérer l’interaction HTML présente de nombreux avantages :

- Le mot de passe (si un a été tapé) n’est jamais stocké par l’application, ni par la bibliothèque d’authentification.
- Permet les redirections vers d’autres fournisseurs d’identité (par exemple, une connexion avec un compte professionnel ou scolaire, ou un compte personnel avec MSAL, ou avec un compte social par Azure AD B2C).
- Permet au STS de contrôler l’accès conditionnel, par exemple, en obligeant l’utilisateur à passer par l’authentification multifacteur (MFA) pendant la phase d’authentification (entrée d’un code confidentiel Windows Hello, ou réponse à un appel sur son téléphone ou sur une application d’authentification sur son téléphone). Lorsque l’authentification multifacteur demandée n’est pas encore configurée, l’utilisateur peut la configurer le moment venu dans la même boîte de dialogue.  L’utilisateur indique son numéro de téléphone mobile et reçoit des instructions pour installer une application d’authentification et lire un code QR permettant d’ajouter son compte. Cette interaction pilotée par serveur est une excellente pratique !
- Permet à l’utilisateur de modifier son mot de passe dans la même boîte de dialogue lorsque le mot de passe a expiré (en fournissant des champs supplémentaires pour l’ancien mot de passe et le nouveau mot de passe).
- Permet de personnaliser la marque du locataire, ou l’application (images) contrôlée par le propriétaire de l’application / l’administrateur de locataires Azure AD.
- Permet aux utilisateurs de donner leur consentement pour laisser l’application accéder aux ressources / étendues en leur nom, juste après l’authentification.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Navigateur web du système sur Xamarin.iOS et Xamarin.Android

Par défaut, MSAL.NET prend en charge le navigateur web du système sur Xamarin.iOS et Xamarin.Android. Pour toutes les plateformes qui fournissent l’interface utilisateur (autrement dit, pas .NET Core), une boîte de dialogue est fournie par la bibliothèque intégrant un contrôle de navigateur web. MSAL.NET utilise également un affichage web intégré pour .NET Desktop et WAB pour la plateforme Windows universelle (UWP). Toutefois, MSAL.NET se sert par défaut du **navigateur web du système** pour les applications Xamarin iOS et Xamarin Android. Sur iOS, son choix peut même se porter sur l’affichage web à utiliser en fonction de la version du système d’exploitation (iOS12, iOS11 et versions antérieures).

L’utilisation du navigateur système présente l’énorme avantage de partager l’état SSO avec d’autres applications et applications web, sans avoir besoin d’un répartiteur (portail d’entreprise / Authenticator). Le navigateur du système a été utilisé, par défaut, dans MSAL.NET pour les plateformes Xamarin iOS et Xamarin Android, car, sur ces plateformes, le navigateur web du système occupe tout l’écran, et l’expérience utilisateur est meilleure. L’affichage web du système ne se différencie pas d’une boîte de dialogue. Sur iOS, en revanche, l’utilisateur peut être amené à devoir donner son consentement pour que le navigateur puisse rappeler l’application, ce qui peut s’avérer ennuyeux.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP n’utilise pas System Webview

Pour les applications de bureau, cependant, lancer un affichage web du système entraîne une expérience utilisateur de moins bonne qualité, car l’utilisateur voit le navigateur, dans lequel il peut déjà avoir d’autres onglets ouverts. Ainsi, lorsque l’authentification a lieu, les utilisateurs obtiennent une page leur demandant de fermer cette fenêtre. Si l’utilisateur n’y prend pas garde, il peut fermer le processus entier (y compris les autres onglets, qui ne sont pas liés à l’authentification). Exploiter le navigateur du système sur un appareil de bureau impliquerait également d’ouvrir des ports locaux et de se mettre à l’écoute sur ces ports, ce qui peut nécessiter des autorisations avancées pour l’application. Vous, en tant que développeur, utilisateur ou administrateur, pouvez être réticent face à cette exigence.

## <a name="enable-embedded-webviews"></a>Activer des affichages web incorporés 
Vous pouvez également activer des affichages web incorporés dans les applications Xamarin.iOS et Xamarin.Android. À compter de MSAL.NET 2.0.0-preview, MSAL.NET prend également en charge l’utilisation de l’option d’affichage web **incorporé**. Pour ADAL.NET, l’affichage web incorporé est la seule option prise en charge.

En tant que développeur qui utilisez MSAL.NET ciblant Xamarin, vous avez le choix entre des affichages web incorporés ou des navigateurs de système. C’est à vous de choisir selon l’expérience utilisateur et les préoccupations en matière de sécurité que vous voulez cibler.

Actuellement, MSAL.NET ne prend pas en charge les répartiteurs iOS et Android. Par conséquent, si vous devez fournir l’authentification unique (SSO), la solution du navigateur système peut demeurer préférable. La prise en charge des répartiteurs avec le navigateur web intégré se trouve sur le backlog MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Différences entre l’affichage web incorporé et le navigateur du système 
Il existe quelques différences visuelles entre la vue web incorporée et le navigateur du système dans MSAL.NET.

**Connexion interactive avec MSAL.NET utilisant l’affichage web incorporé :**

![affichage incorporé](media/msal-net-web-browsers/embedded-webview.png)

**Connexion interactive avec MSAL.NET utilisant le navigateur du système :**

![Navigateur du système](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Options pour développeurs

En tant que développeur utilisant MSAL.NET, vous disposez de plusieurs options pour afficher la boîte de dialogue interactive à partir de STS :

- **Navigateur système.** Le navigateur du système est défini par défaut dans la bibliothèque. Si vous utilisez Android, consultez les [navigateurs de système](msal-net-system-browser-android-considerations.md) pour plus d’informations sur les navigateurs pris en charge pour l’authentification. Lorsque vous utilisez le navigateur système dans Android, nous vous conseillons de préférer l’appareil qui dispose d’un navigateur prenant en charge les onglets personnalisés de Chrome.  Sinon, l’authentification peut échouer.
- **Affichage web incorporé.** Pour utiliser uniquement la vue web incorporée dans MSAL.NET, le générateur de paramètres `AcquireTokenInteractively` contient une méthode `WithUseEmbeddedWebView()`.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android :

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Choix entre le navigateur web incorporé et le navigateur du système sur Xamarin.iOS

Dans le fichier `AppDelegate.cs` de votre application iOS, vous pouvez initialiser la valeur de `ParentWindow` sur `null`. Ce n’est pas utilisé dans iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Choix entre le navigateur web incorporé et le navigateur du système sur Xamarin.Android

Dans le fichier `MainActivity.cs` de votre application Android, vous pouvez définir l’activité parente, afin que les résultats de l’authentification y soient retournés :

```csharp
 App.ParentWindow = this;
```

Ensuite, dans le fichier `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Détection de la présence des onglets personnalisés sur Xamarin.Android

Si vous souhaitez utiliser le navigateur web du système pour activer l’authentification unique avec les applications qui s’exécutent dans le navigateur, mais que vous êtes soucieux par rapport à l’expérience utilisateur des appareils Android qui ne disposent pas de navigateur avec prise en charge des onglets personnalisés, vous avez la possibilité de décider en appelant la méthode `IsSystemWebViewAvailable()` dans `IPublicClientApplication`. Cette méthode retourne `true` si PackageManager détecte des onglets personnalisés, et `false` s’ils ne sont pas détectés sur l’appareil.

Selon la valeur retournée par cette méthode, et vos exigences, vous pouvez prendre une décision :

- Vous pouvez retourner un message d’erreur personnalisé à l’utilisateur. Par exemple :  « Veuillez installer Chrome pour poursuivre l’authentification » OU
- Vous pouvez revenir à l’option de l’affichage web incorporé et lancer l’interface utilisateur en tant que vue web incorporée.

Le code ci-dessous montre l’option d’affichage web incorporé :

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET Core ne prend pas en charge l’authentification interactive fournie au départ

Pour .NET Core, l’acquisition de jetons de manière interactive n’est pas disponible. De fait, .NET Core ne fournit pas d’interface utilisateur pour l’instant. Si vous souhaitez apporter une connexion interactive à une application .NET Core, vous pouvez laisser l’application présenter un code et une URL à l’utilisateur pour qu’il puisse se connecter de manière interactive (voir [Flux de code d’appareil](msal-authentication-flows.md#device-code)).

Sinon, vous pouvez implémenter l’interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) et fournir votre propre navigateur