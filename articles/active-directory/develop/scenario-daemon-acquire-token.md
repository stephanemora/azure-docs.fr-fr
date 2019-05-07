---
title: Appel d’application démon web API (l’acquisition des jetons pour l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon par appels web API (l’acquisition de jetons)
services: active-directory
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075369"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Application démon qui appelle des API - web acquérir un jeton

Une fois que l’application de client confidentiel est construite, vous pouvez acquérir un jeton pour l’application en appelant ``AcquireTokenForClient``, en passant l’étendue et forcer ou pas une actualisation du jeton.

## <a name="scopes-to-request"></a>Demander des étendues

L’étendue à la demande pour un flux d’informations d’identification de client est le nom de la ressource suivie `/.default`. Cette notation indique à Azure AD à utiliser le **les autorisations de niveau application** déclaré statiquement lors de l’inscription de l’application. En outre, comme vu précédemment, ces autorisations d’API doivent être accordées par un administrateur client

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

Dans la bibliothèque MSAL. Python, le fichier de configuration ressemblerait à l’extrait de code suivant :

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Tous

La portée utilisée pour les informations d’identification du client doit toujours être resourceId + « / .default »

### <a name="case-of-v10-resources"></a>Cas de ressources de la version 1.0

> [!IMPORTANT]
> Pour la bibliothèque MSAL (point de terminaison v2.0) demandant un jeton d’accès pour une ressource acceptant un jeton d’accès v1.0, Azure AD analyse l’audience souhaitée à partir de la portée demandée en prenant toutes les modifications avant la dernière barre oblique et en l’utilisant comme l’identificateur de ressource.
> Par conséquent, if, telles que SQL Azure (**https://database.windows.net**) la ressource attend un public se terminant par une barre oblique (pour SQL Azure : `https://database.windows.net/`), vous aurez besoin demander une étendue de `https://database.windows.net//.default` (Notez la double barre oblique). Voir aussi MSAL.NET problème [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Barre oblique de fin d’url de ressource est omise, ce qui a provoqué d’échec d’authentification sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Cache de jeton d’application

Dans MSAL.NET, `AcquireTokenForClient` utilise le **cache de jeton d’application** (toutes les autres méthodes AcquireTokenXX permet d’utiliser le cache de jetons d’utilisateur) n’appelez pas `AcquireTokenSilent` avant d’appeler `AcquireTokenForClient` comme `AcquireTokenSilent` utilise le **utilisateur** cache des jetons. `AcquireTokenForClient` vérifie la **application** jeton cache lui-même et met à jour.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Si vous n’avez pas encore d’une bibliothèque pour le langage de votre choix, vous souhaiterez peut-être utiliser le protocole directement :

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Premier cas : Requête de jeton d’accès avec un secret partagé

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Deuxième cas : Requête de jeton d’accès avec un certificat

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>En savoir plus sur le protocole

Pour plus d’informations, consultez la documentation du protocole : [Flux d’informations d’identification Azure Active Directory v2.0 et le client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="did-you-use-the-resourcedefault-scope"></a>Vous avez peut-être utilisé l’étendue de la ressource/.default ?

Si vous obtenez un message d’erreur indiquant que vous avez utilisé une portée non valide, vous n’avez pas probablement utiliser le `resource/.default` étendue.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Vous avez oublié de fournir le consentement de l’administrateur ? Applications de démon en avez besoin !

Si vous obtenez une erreur lors de l’appel de l’API **privilèges insuffisants pour terminer l’opération**, l’administrateur du client doit accorder des autorisations à l’application. Consultez l’étape 6 d’inscrire l’application cliente ci-dessus.
Vous verrez généralement et erreur telles que la description d’erreur suivant :

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - appeler une API web](scenario-daemon-call-api.md)