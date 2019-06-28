---
title: Application démon conçue pour appeler des API web (acquisition de jetons pour l'application) - Plateforme d'identités Microsoft
description: Apprenez à générer une application démon qui appelle des API web (acquisition de jetons)
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075369"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Application démon conçue pour appeler des API web - acquisition d'un jeton

Une fois l'application cliente confidentielle générée, vous pouvez acquérir un jeton pour celle-ci en appelant ``AcquireTokenForClient``, en transmettant l'étendue, et en forçant ou non une actualisation du jeton.

## <a name="scopes-to-request"></a>Étendues à demander

L'étendue à demander pour un flux d'informations d'identification client est le nom de la ressource suivi de `/.default`. Cette notation indique à Azure AD d'utiliser les **autorisations de niveau application** déclarées de manière statique lors de l'inscription de l'application. En outre, comme indiqué précédemment, ces autorisations d'API doivent être accordées par un administrateur client.

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

Dans MSAL.Python, le fichier de configuration serait semblable à l'extrait de code suivant :

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

L'étendue utilisée pour les informations d'identification client doit toujours être resourceId+"/.default".

### <a name="case-of-v10-resources"></a>Cas des ressources v1.0

> [!IMPORTANT]
> Concernant le point de terminaison MSAL (v2.0) qui demande un jeton d'accès pour une ressource acceptant un jeton d'accès v1.0, Azure AD analyse l'audience souhaitée d'après l'étendue demandée en prenant tout ce qui précède la dernière barre oblique et en l'utilisant comme identificateur de la ressource.
> Par conséquent, si, comme Azure SQL ( **https://database.windows.net** ), la ressource attend une audience se terminant par une barre oblique (pour Azure SQL : `https://database.windows.net/` ), vous devez demander une étendue de `https://database.windows.net//.default` (notez la double barre oblique). Voir aussi le problème MSAL.NET [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747) : la barre oblique de fin a été omise dans l'URL de la ressource, ce qui a entraîné un échec d'authentification sql.

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

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

#### <a name="application-token-cache"></a>Cache de jetons d'application

Dans MSAL.NET, `AcquireTokenForClient` utilise le **cache de jetons d'application** (toutes les autres méthodes AcquireTokenXX utilisent le cache de jetons d'utilisateur). N'appelez pas `AcquireTokenSilent` avant d'appeler `AcquireTokenForClient` car `AcquireTokenSilent` utilise le cache de jetons d'**utilisateur**. `AcquireTokenForClient` vérifie le cache de jetons d'**application** et le met à jour.

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

### <a name="protocol"></a>Protocole

Si vous n'avez pas encore de bibliothèque pour la langue de votre choix, vous pouvez directement utiliser le protocole :

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

Pour plus d'informations, consultez la documentation du protocole : [Azure Active Directory v2.0 et le flux d'informations d'identification du client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="did-you-use-the-resourcedefault-scope"></a>Avez-vous utilisé l'étendue resource/.default ?

Si vous recevez un message d'erreur indiquant que vous avez utilisé une étendue non valide, cela signifie probablement que vous n'avez pas utilisé l'étendue `resource/.default`.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Avez-vous oublié de fournir le consentement administrateur ? Les applications démon en ont besoin !

Si vous rencontrez l'erreur **Privilèges insuffisants pour effectuer l'opération** lors de l'appel de l'API, l'administrateur client doit accorder des autorisations à l'application. Consultez l'étape 6 de la section Inscrire l'application cliente ci-dessus.
Vous rencontrerez généralement une erreur semblable à la description d'erreur suivante :

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