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
ms.openlocfilehash: bc3881f60ba77569ae0d2ae775ad124d0587fa82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524047"
---
# <a name="configure-authentication-options-in-a-wpf-desktop-application-using-azure-active-directory-b2c"></a>Configurer les options d’authentification dans une application de bureau WPF avec Azure Active Directory B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application de bureau WPF. Avant de commencer, lisez l’article [Configurer l’authentification dans un exemple d’application de bureau WPF](configure-authentication-sample-wpf-desktop-app.md).


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Accédez à votre objet de configuration MSAL et ajoutez la méthode `withLoginHint()` avec l’indicateur de connexion.

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet de liste ou utilisez un objet `Dictionary` existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez au dictionnaire le paramètre `domain_hint` avec le nom de domaine correspondant. Par exemple : `facebook.com`.
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
1. Ajoutez au dictionnaire le paramètre `ui_locales` avec le code de langue correspondant. Par exemple : `en-us`.
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
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définissez la valeur du paramètre. Par exemple : `germany-promotion`.
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
1. Dans votre code, générez ou obtenez un jeton d’ID, puis définissez le jeton sur une variable. Par exemple : `idToken`. 
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

## <a name="configure-redirect-uri"></a>Configurer un URI de redirection

Quelques points importants sont à prendre en compte lorsque vous choisissez un URI de redirection dans l’[inscription d’une application de bureau](configure-authentication-sample-wpf-desktop-app.md#23-register-the-desktop-app) :

* **Développement** Pour le développement et les **applications de bureau**, vous pouvez affecter la valeur `http://localhost` à l’URI de redirection. Azure AD B2C respecte tous les ports de la requête. Si l’URI inscrit contient un port, Azure AD B2C utilisera uniquement ce port. Par exemple, si l’URI de redirection inscrit est `http://localhost`, l’URI de redirection dans la requête peut être `http://localhost:<randomport>`. Si l’URI de redirection inscrit est `http://localhost:8080`, l’URI de redirection dans la requête doit être `http://localhost:8080`.
* **Unique** : le schéma de l'URI de redirection doit être propre à chaque application. Dans l’exemple `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l'utilisateur fait le mauvais choix, la connexion échoue.
* **Complet** : l'URI de redirection doit être doté d'un schéma et d'un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//oauth/` fonctionne tandis que `//oauth` échoue. N'incluez pas de caractères spéciaux, tels que des traits de soulignement, dans l'URI.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus : [MSAL pour les options de configuration .NET, UWP, NetCore et Xamarin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
