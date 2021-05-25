---
title: Demandes et défis liés aux revendications
titleSuffix: Microsoft identity platform
description: Explication relative aux demandes et défis liés aux revendications sur la plateforme d’identité Microsoft
services: active-directory
author: knicholasa
manager: martinco
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: nichola
ms.reviewer: kkrishna, kylemar
ms.openlocfilehash: bf3b6db0bcb5478412837a2fa43f610dbf8690d6
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109795431"
---
# <a name="claims-challenges-and-claims-requests"></a>Demandes et défis liés aux revendications

Un **défi de revendications** est une réponse envoyée à partir d’une API indiquant qu’un jeton d’accès envoyé par une application cliente ne dispose pas de revendications suffisantes. Cela peut être dû au fait que le jeton ne respecte pas les stratégies d’accès conditionnel définies pour cette API ou que le jeton d’accès a été révoqué.

Une **demande de revendications** est effectuée par l’application cliente pour rediriger l’utilisateur vers le fournisseur d’identité afin de récupérer un nouveau jeton doté des revendications répondant aux exigences supplémentaires non satisfaites.

Les applications utilisant des fonctionnalités de sécurité améliorées, comme l’[Évaluation continue de l'accès](../conditional-access/concept-continuous-access-evaluation.md) et le [contexte d’authentification de l’accès conditionnel](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775) doivent être préparées pour gérer les défis liés aux revendications.

Votre application recevra uniquement les défis liés aux revendications si elle déclare qu’elle peut les gérer à l’aide des **fonctionnalités du client**.

Pour recevoir des informations indiquant si les applications clientes peuvent gérer les demandes de revendications, un implémenteur d’API doit demander **xms_cc** comme revendication facultative dans son manifeste d’application.

## <a name="claims-challenge-header-format"></a>Format d’en-tête de demande de revendications

Le défi de revendications est une directive dans l’en-tête www-authenticate renvoyé par une API lorsqu’un jeton d’accès n’est pas autorisé et qu’un nouveau jeton d’accès est requis. Le défi de revendications comprend plusieurs parties : le code d’état HTTP de la réponse et l’en-tête www-authenticate, qui possède lui-même plusieurs parties et doit contenir une directive de revendications.

``` https
HTTP 401; Unauthorized

www-authenticate =Bearer realm="", authorization_uri="https://login.microsoftonline.com/common/oauth2/authorize", error="insufficient_claims", claims="eyJhY2Nlc3NfdG9rZW4iOnsiYWNycyI6eyJlc3NlbnRpYWwiOnRydWUsInZhbHVlIjoiYzEifX19"
```

 **Code d’état HTTP** : doit être **401 non autorisé**.

**En-tête de réponse www-authenticate** contenant :

| Paramètre    | Obligatoire ou facultatif | Description |
|--------------|-------------|--------------|
| Type d'authentification | Obligatoire | Doit correspondre à **Porteur.**|
| Realm | Facultatif | ID ou nom de domaine du locataire (par exemple, microsoft.com) faisant l’objet d’un accès. DOIT être une chaîne vide dans le cas où l’authentification transite via le [point de terminaison commun](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common). |
| `authorization_uri` | Obligatoire | URI du point de terminaison d’autorisation dans lequel une authentification interactive peut être effectuée, si nécessaire. Si elles sont spécifiées dans le domaine, les informations du locataire doivent être incluses dans authorization_uri. Si le domaine est une chaîne vide, authorization_uri DOIT être en rapport avec le [point de terminaison commun](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common). |
| `error` | Obligatoire | Doit correspondre à « insufficient_claims » lorsqu’une demande de revendications doit être générée. | 
| `claims` | Obligatoire lorsque l’erreur correspond à « insufficient_claims ». | Chaîne entre guillemets contenant une [demande de revendications](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter)encodée en base 64. La demande de revendications doit demander des revendications pour « access_token » au niveau supérieur de l’objet JSON. La valeur (revendications demandées) dépend du contexte et sera spécifiée plus loin dans ce document. Pour des raisons de taille, les applications de partie de confiance DOIVENT réduire le JSON avant l’encodage en base 64. Le JSON brut de l’exemple ci-dessus est {"access_token":{"acrs":{"essential":true,"value":"cp1"}}}. |

La réponse 401 peut contenir plusieurs en-têtes www-authenticate. Tous les champs ci-dessus doivent être contenus dans le même en-tête www-authenticate. L’en-tête www-authenticate avec la demande de revendications PEUT contenir d’autres champs. Les champs de l’en-tête ne sont pas ordonnés. Conformément à RFC 7235, chaque nom de paramètre ne doit intervenir qu’une seule fois par demande de schéma d’authentification.

## <a name="claims-request"></a>Demande de revendications

Lorsqu’une application reçoit un défi de revendications indiquant que le jeton d’accès précédent n’est plus considéré comme valide, cette application doit effacer le jeton de n’importe quel cache local ou session utilisateur. Elle doit ensuite rediriger l’utilisateur connecté vers Azure AD pour récupérer un nouveau jeton à l’aide du [flux de code d’autorisation OAuth 2.0](v2-oauth2-auth-code-flow.md) avec un paramètre de **revendications** répondant aux exigences supplémentaires non satisfaites.

Un exemple est fourni ci-dessous .

``` https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
claims=%7B%22access_token%22%3A%7B%22acrs%22%3A%7B%22essential%22%3Atrue%2C%22value%22%3A%22urn%3Amicrosoft%3Areq1%22%7D%7D%7D
```

Le défi de revendications doit être transmis dans le cadre de tous les appels au point de terminaison [/authorize](v2-oauth2-auth-code-flow.md#request-an-authorization-code) d’Azure AD jusqu’à ce qu’un jeton soit récupéré avec succès, après quoi il n’est plus nécessaire.

Pour remplir le paramètre de revendications, le développeur doit :

1. Décoder la chaîne reçue précédemment en base 64.
2. Encoder la chaîne en URL et l’ajouter à nouveau au paramètre de **revendications**.

Une fois ce flux terminé, l’application reçoit un jeton d’accès contenant les revendications supplémentaires attestant du fait que l’utilisateur a rempli les conditions requises.

## <a name="client-capabilities"></a>Fonctionnalités du client

Votre application recevra uniquement les défis liés aux revendications si elle déclare qu’elle peut les gérer à l’aide des **fonctionnalités du client**.

Pour éviter tout trafic supplémentaire ou impact sur l’expérience utilisateur, Azure AD ne suppose pas que votre application peut gérer les revendications testées, sauf si vous l’indiquez explicitement. Une application ne recevra pas de défis de revendications (et ne sera pas en mesure d’utiliser les fonctionnalités associées telles que les jetons CAE), sauf si elle est prête à les gérer avec la fonctionnalité « cp1 ».

### <a name="how-to-communicate-client-capabilities-to-azure-ad"></a>Comment communiquer des fonctionnalités clientes à Azure AD

L’exemple de paramètre de revendications suivant montre comment une application cliente communique sa fonctionnalité à Azure AD dans un [flux de code d’autorisation OAuth 2.0](v2-oauth2-auth-code-flow.md).

```json
Claims: {"access_token":{"xms_cc":{"values":["cp1"]}}}
```

Les utilisateurs de la bibliothèque MSAL ont recours au code suivant :

```c#
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
 .WithDefaultRedirectUri()
 .WithAuthority(authority)
 .WithClientCapabilities(new [] {"cp1"})
 .Build();*
```

Les utilisateurs de Microsoft.Identity.Web peuvent ajouter le code suivant au fichier de configuration :

```c#
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    // the remaining settings
    // ... 
    "ClientCapabilities": [ "cp1" ]
},
```

Une demande faite à Azure AD se présente comme suit :

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
&claims=%7B%22access_token%22%3A%7B%22xms_cc%22%3A%7B%22values%22%3A%5B%22cp1%22%5D%7D%7D%7D
```

Si vous disposez déjà d’une charge utile pour le paramètre de revendications, vous devez l’ajouter à l’ensemble existant.

Par exemple, si vous avez déjà la réponse suivante à partir d’une opération de contexte d’authentification d’accès conditionnel

```json
{"access_token":{"acrs":{"essential":true,"value":"c25"}}}
```

Vous devez ajouter la fonctionnalité cliente à la charge utile existante des **revendications**.

```json
{"access_token":{"xms_cc":{"values":["cp1"]},"acrs":{"essential":true,"value":"c25"}}}
```

## <a name="receiving-xms_cc-claim-in-an-access-token"></a>Réception d’une revendication xms_cc dans un jeton d’accès

Pour recevoir des informations indiquant si les applications clientes peuvent gérer les demandes de revendications, un implémenteur d’API doit demander **xms_cc** comme revendication facultative dans son manifeste d’application.

La revendication **xms_cc** dotée de la valeur « cp1 » dans le jeton d’accès est la manière faisant autorité pour identifier une application cliente capable de gérer une demande de revendications. **xms_cc** est une revendication facultative qui n’est pas toujours émise dans le jeton d’accès, même si le client envoie une demande de revendications avec « xms_cc ». Pour qu’un jeton d’accès contienne la revendication **xms_cc**, l’application de ressource (c’est-à-dire, l’implémenteur d’API) doit demander xms_cc comme [revendication facultative](active-directory-optional-claims.md) dans son manifeste d’application. En cas de demande en tant que revendication facultative, **xms_cc** sera ajouté au jeton d’accès uniquement si l’application cliente envoie **xms_cc** dans la demande de revendications. La valeur de la demande de revendications **xms_cc** sera incluse comme valeur de la revendication **xms_cc** dans le jeton d’accès, s’il s’agit d’une valeur connue. La seule valeur actuellement connue est **cp1**.

Les valeurs ne respectent pas la casse et ne sont pas ordonnées. Si plusieurs valeurs sont spécifiées dans la demande de revendications **xms_cc**, ces valeurs forment une collection à valeurs multiples comme valeur de la revendication **xms_cc**.

Une demande de :

```json
{ "access_token": { "xms_cc":{"values":["cp1","foo", "bar"] } }}
```

entraîne une revendication de

```json
"xms_cc": ["cp1", "foo", "bar"]
```

dans le jeton d’accès, si **cp1**, **foo** et **bar** sont des fonctionnalités connues.

Voici à quoi ressemble le manifeste de l’application une fois la **revendication facultative** [xms_cc](active-directory-optional-claims.md) demandée

```c#
"optionalClaims":
{
    "accessToken": [
    {
        "additionalProperties": [],
        "essential": false,
        "name": "xms_cc",
        "source": null
    }],
    "idToken": [],
    "saml2Token": []
}
```

L’API peut ensuite personnaliser les réponses selon la capacité du client à gérer ou non les revendications.

Exemple en C#

```c#
Claim ccClaim = context.User.FindAll(clientCapabilitiesClaim).FirstOrDefault(x => x.Type == "xms_cc");
if (ccClaim != null && ccClaim.Value == "cp1")
{
    // Return formatted claims challenge as this client understands this
}
else
{
    // Throw generic exception
    throw new UnauthorizedAccessException("The caller does not meet the authentication bar to carry our this operation. The service cannot allow this operation");
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Plateforme d’identités Microsoft et flux de code d’autorisation OAuth](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- [Guide pratique pour utiliser des API dotées d’Évaluation continue de l’accès dans vos applications](app-resilience-continuous-access-evaluation.md)
- [Accès conditionnel granulaire pour les actions et les données sensibles](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
