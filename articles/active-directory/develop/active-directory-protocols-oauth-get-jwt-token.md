---
title: S’authentifier auprès d’Azure AD et obtenir un jeton JWT à l’aide d’OAuth 2.0
description: Exemple de code montrant comment s’authentifier auprès d’Azure Active Directory à l’aide d’OAuth 2.0 pour accéder aux API web et applications web sécurisées dans votre organisation.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: d77af898d5baef4fa7970132b0eb8deddb8f68cb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981795"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Demander un jeton d’accès à l’aide d’OAuth 2.0 pour accéder aux applications et API web sécurisées par Azure Active Directory

Cet article explique comment obtenir un jeton web JSON (JWT) pour accéder aux ressources sécurisées par Azure AD. Il suppose que vous disposez d’un [jeton d’autorisation](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) provenant d’une autorisation accordée à un utilisateur ou obtenu par le biais d’un [principal du service](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Générer la demande

Utilisez la requête HTTP `POST` suivante pour obtenir un jeton JWT et accéder à une application ou API spécifique sécurisée par Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>En-têtes de requête

Les en-têtes suivants sont requis :

|En-tête de requête|Description|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Content-Type :*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>Paramètres URI

| Paramètre     |                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| locataire        | required              | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | required              | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) a assigné à votre application.                                                                                                                                                                                                                             |
| grant_type    | required              | Doit être `authorization_code` pour le flux de code d'autorisation.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | required              | Une liste d’étendues séparées par des espaces. Les étendues demandées dans ce tronçon doivent être équivalentes ou correspondre à un sous-ensemble des étendues dans l’appel à `/authorize`. Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie alors un jeton pour la ressource spécifiée dans la première étendue. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](v2-permissions-and-consent.md). |
| code          | required              | Le code d’autorisation acquis dans l’appel à `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | required              | Valeur redirect_uri qui a déjà été utilisée pour obtenir le paramètre authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Ne l’utilisez pas dans une application native, car les secrets clients ne peuvent pas être stockés de manière fiable sur les appareils. Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur.  Les secrets clients doivent être encodés en URL avant d’être envoyés.                                                                                 |
| code_verifier | facultatif              | Le même code_verifier utilisé pour obtenir le authorization_code. Obligatoire si PKCE est utilisé dans la requête d’octroi du code d’autorisation. Pour plus d'informations, consultez le [RFC PKCE](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>Gérer la réponse

Une réponse de jeton correcte contient un jeton JWT et doit ressembler à l’extrait suivant :

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | [Jeton d’accès](access-tokens.md) demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, par exemple une API Web.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur.                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | La durée de validité (en secondes) du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | L’étendue de validité du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour en savoir plus, consultez la page de [référence sur l’octroi du code v2.0](v2-oauth2-auth-code-flow.md#refresh-the-access-token). <br> **Remarque :** Fourni uniquement si l’étendue `offline_access` a été demandée.                                               |
| id_token      | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur id_tokens, consultez [`id_token reference`](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |



