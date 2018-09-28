---
title: 'Démarrage rapide : Application UWP Windows Azure AD v2 | Microsoft Docs'
description: Découvrez comment une application de plateforme Windows universelle (XAML) peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison Azure Active Directory v2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970817"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (UWP)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ce démarrage rapide contient un exemple de code qui montre comment une application de plateforme Windows universelle (UWP) peut connecter des utilisateurs avec des comptes personnels, professionnels et scolaires, obtenir un jeton d'accès et appeler l’API Graph Microsoft.

![Fonctionnement de l’exemple d’application généré par ce démarrage rapide](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Inscrire et télécharger
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Inscrire et configurer votre exemple de code et d’application
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrire votre application
> Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :
> 1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
> 1. Dans la zone **Nom de l’application**, attribuez un nom à votre application.
> 1. Vérifiez que la case **Guided Setup** (Installation guidée) est décochée et sélectionnez **Créer**.
> 1. Sélectionnez **Ajouter une plateforme**, puis **Application native**, et cliquez sur **Enregistrer**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Étape 1 : Configurer votre application
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter une URL de redirection telle que **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-uwp/green-check.png) Votre application est configurée avec ces attributs

#### <a name="step-2-download-your-visual-studio-project"></a>Étape 2 : Télécharger votre projet Visual Studio

 - [Télécharger le projet Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Étape 3 : configurer votre projet Visual Studio

1. Extrayez le fichier zip dans un dossier local (par exemple, **C:\Azure-Samples**)
1. Ouvrez le projet dans Visual Studio
1. Dans **App.Xaml.cs**, remplacez la ligne commençant par `private static string ClientId` par :

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>Informations complémentaires

Vue d’ensemble de ce démarrage rapide ci-dessous :

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) est la bibliothèque utilisée pour connecter des utilisateurs et demander des jetons permettant d’accéder à une API protégée par Microsoft Azure Active Directory. Vous pouvez l’installer en exécutant la commande suivante dans la *console du gestionnaire de package* de Visual Studio :

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant la ligne ci-dessous :

```csharp
using Microsoft.Identity.Client;
```

Ensuite, initialisez MSAL en utilisant la ligne ci-dessous :

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Où : ||
> |---------|---------|
> |ClientId | L’ID d’application de l’application inscrite dans *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Demande de jetons

MSAL utilise deux méthodes pour acquérir des jetons : `AcquireTokenAsync` et `AcquireTokenSilentAsync` :

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

 Certaines situations nécessitent de forcer des utilisateurs à interagir avec un point de terminaison Azure Active Directory v2 via une fenêtre contextuelle pour valider leurs informations d’identification ou donner un consentement. Voici quelques exemples :

- La première connexion des utilisateurs à l’application
- Les utilisateurs doivent réentrer leurs informations d’identification, car le mot de passe a expiré.
- Votre application demande l’accès à une ressource pour laquelle l’utilisateur doit donner son consentement.
- Une authentification à 2 facteurs est demandée.

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Où :||
> |---------|---------|
> |étendues | Contient les étendues demandées (par exemple, `{ "user.read" }` pour Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` pour les API web personnalisées) |

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

Vous ne souhaitez pas demander à l’utilisateur de valider ses informations d’identification chaque fois qu’il doit accéder à une ressource. Vous préférez que les acquisitions et les renouvellements de jetons s’effectuent sans interaction de l’utilisateur. `AcquireTokenSilentAsync` est la méthode généralement utilisée pour obtenir les jetons utilisés pour accéder à des ressources protégées après la méthode `AcquireTokenAsync` initiale :

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Où : ||
> |---------|---------|
> |étendues | Contient les étendues demandées (par exemple, `{ "user.read" }` pour Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` pour les API web personnalisées) |
> |accounts.FirstOrDefault() | Le premier utilisateur dans le cache (MSAL prend en charge plusieurs utilisateurs dans une seule application) |

## <a name="next-steps"></a>Étapes suivantes

Essayez le didacticiel de bureau Windows pour apprendre à créer, étape par étape, des applications et des fonctionnalités, y compris une explication complète de ce démarrage rapide :

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Découvrez les étapes permettant de créer l’application utilisée dans ce démarrage rapide

> [!div class="nextstepaction"]
> [Didacticiel UWP : Appeler l’API Graph](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]