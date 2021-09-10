---
title: Activer les options d’application de bureau WPF à l’aide d’Azure Active Directory B2C
description: Activez l’utilisation des options d’application de bureau WPF à l’aide de plusieurs méthodes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: bbb0b9d29b2551a6c733d0df8b62505efe68514f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220300"
---
# <a name="enable-authentication-options-in-a-wpf-desktop-app-by-using-azure-ad-b2c"></a>Activer les options d’authentification dans une application de bureau WPF avec Azure AD B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application de bureau WPF. 

Avant de commencer, familiarisez-vous en lisant l’article [Configurer l’authentification dans un exemple d’application de bureau WPF à l’aide d’Azure AD B2C](configure-authentication-sample-wpf-desktop-app.md).


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la requête d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Recherchez votre objet de configuration de la Bibliothèque d’authentification Microsoft (MSAL), puis ajoutez la méthode `withLoginHint()` avec l’indicateur de connexion.

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet de liste ou utilisez un objet `Dictionary` existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez au dictionnaire le paramètre `domain_hint` avec le nom de domaine correspondant (par exemple, `facebook.com`).
1. Passez l’objet des paramètres de requête supplémentaires dans la méthode `WithExtraQueryParameters` de l’objet de configuration MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Créez un objet de liste ou utilisez un objet `Dictionary` existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez au dictionnaire le paramètre `ui_locales` avec le code de langue correspondant (par exemple, `en-us`).
1. Passez l’objet des paramètres de requête supplémentaires dans la méthode `WithExtraQueryParameters` de l’objet de configuration MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez un objet de liste ou utilisez un objet `Dictionary` existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définir la valeur du paramètre (par exemple `germany-promotion`).
1. Passez l’objet des paramètres de requête supplémentaires dans la méthode `WithExtraQueryParameters` de l’objet de configuration MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Dans votre code, générez ou obtenez un jeton d’ID, puis définissez le jeton sur une variable (par exemple `idToken`). 
1. Créez un objet de liste ou utilisez un objet `Dictionary` existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre `id_token_hint` avec la variable correspondante qui stocke le jeton d’ID.
1. Passez l’objet des paramètres de requête supplémentaires dans l’attribut `extraQueryParameters` de l’objet de configuration MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


L’extrait de code suivant montre comment configurer la journalisation MSAL :

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-the-redirect-uri"></a>Configurer l’URI de redirection

Pendant le processus [d’inscription de l’application de bureau](configure-authentication-sample-wpf-desktop-app.md#step-23-register-the-desktop-app), lorsque vous choisissez un URI de redirection, gardez à l’esprit les considérations importantes suivantes :

* **Développement** : Pour l’utilisation du développement dans les applications de bureau, vous pouvez définir l’URI de redirection sur `http://localhost`, ainsi Azure AD B2C respectera tous les ports de la requête. Si l’URI inscrit contient un port, Azure AD B2C utilisera uniquement ce port. Par exemple, si l’URI de redirection inscrit est `http://localhost`, l’URI de redirection dans la requête peut être `http://localhost:<randomport>`. Si l’URI de redirection inscrit est `http://localhost:8080`, l’URI de redirection dans la requête doit être `http://localhost:8080`.
* **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, les utilisateurs peuvent choisir entre plusieurs applications. Si les utilisateurs choisissent de manière incorrecte, la connexion échoue.
* **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne et `//oauth` échoue. N'incluez pas de caractères spéciaux dans l'URI. Par exemple, le caractère de soulignement (_) n’est pas autorisé.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus : consultez [MSAL pour les options de configuration .NET, UWP, NetCore et Xamarin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki).
