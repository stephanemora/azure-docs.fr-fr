---
title: Demander un jeton d’accès - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment demander un jeton d’accès à partir d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5670d8b3c97cc1f9f6d149e8eadaa60d527e45f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60361113"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Demande un jeton d’accès dans Azure Active Directory B2C

Un *jeton d’accès* contient des revendications que vous pouvez utiliser dans Azure Active Directory (Azure AD) B2C pour identifier les autorisations accordées à votre API. Lors de l’appel d’un serveur de ressources, un jeton d’accès doit être présent dans la requête HTTP. Un jeton d’accès est désigné comme **access_token** dans les réponses à partir d’Azure AD B2C. 

Cet article vous montre comment demander un jeton d’accès pour une application web et l’API web. Pour plus d’informations sur les jetons dans Azure AD B2C, consultez le [vue d’ensemble de jetons dans Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Les chaînes d’API web (pour le compte de) ne sont pas prises en charge par Azure AD B2C.** -De nombreuses architectures incluent une API web qui doit appeler une autre API, toutes deux sécurisées par Azure AD B2C de web en aval. Ce scénario est courant dans les clients disposant d’une API web principale, qui à son tour appelle un autre service. Ce scénario d’API web chaînée peut être pris en charge à l’aide de l’octroi des informations d’identification du porteur OAuth 2.0 JWT, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n’est pas implémenté dans Azure AD B2C pour l’instant.

## <a name="prerequisites"></a>Conditions préalables

- [Créez un flux d'utilisateurs](tutorial-create-user-flows.md) pour permettre aux utilisateurs de s'inscrire et de se connecter à votre application.
- Si vous n’avez pas déjà fait, [ajouter une application API à votre locataire Azure Active Directory B2C web](add-web-application.md).

## <a name="scopes"></a>Étendues

Étendues offrent un moyen de gérer les autorisations à des ressources protégées. Lorsqu’un jeton d’accès est demandé, l’application cliente doit spécifier les autorisations souhaitées dans le **étendue** paramètre de la requête. Par exemple, pour spécifier le **valeur d’étendue** de `read` pour l’API qui a le **URI ID d’application** de `https://contoso.onmicrosoft.com/api`, l’étendue serait `https://contoso.onmicrosoft.com/api/read`.

Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, les utilisateurs de l’API web peuvent avoir un accès en lecture et en écriture, ou les utilisateurs de l’API web peuvent avoir l’accès en lecture uniquement. Pour acquérir plusieurs autorisations dans la même requête, vous pouvez ajouter plusieurs entrées dans le seul **étendue** paramètre de la requête, séparée par des espaces.

L’exemple suivant montre des étendues décodées dans une URL :

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

L’exemple suivant montre des étendues codés dans une URL :

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Si vous demandez plus étendues que ce qui est accordé pour votre application cliente, l’appel réussit si au moins une autorisation est accordée. Le **scp** revendication dans le jeton d’accès résultant est remplie uniquement avec les autorisations qui ont été accordées avec succès. La norme OpenID Connect spécifie plusieurs valeurs de portée spéciale. Les étendues suivantes représentent l’autorisation d’accès au profil de l’utilisateur :

- **OpenID** -demande un jeton d’ID.
- **offline_access** -demande un jeton de l’actualisation à l’aide [flux du Code d’authentification](active-directory-b2c-reference-oauth-code.md).

Si le **response_type** paramètre dans un `/authorize` demande inclut `token`, le **étendue** paramètre doit inclure cette étendue d’au moins une ressource autre que `openid` et `offline_access`qui sera accordée. Sinon, le `/authorize` demande échoue.

## <a name="request-a-token"></a>Demander un jeton

Pour demander un jeton d’accès, vous avez besoin d’un code d’autorisation. Voici un exemple d’une demande à la `/authorize` point de terminaison pour un code d’autorisation. Domaines personnalisés ne sont pas pris en charge pour une utilisation avec des jetons d’accès. Utiliser votre domaine de locataire-name.onmicrosoft.com dans l’URL de requête.

Dans l'exemple suivant, vous remplacez ces valeurs :

- `<tenant-name>` - Nom de votre locataire Azure AD B2C.
- `<policy-name>` - Nom de votre stratégie personnalisée ou de votre flux d'utilisateurs.
- `<application-ID>` -L’identificateur d’application de l’application web que vous avez inscrite pour prendre en charge le flux de l’utilisateur.
- `<redirect-uri>` - **URI de redirection** que vous avez entré lorsque vous avez inscrit l'application cliente.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

La réponse avec le code d’autorisation doit être similaire à cet exemple :

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Après avoir correctement reçu le code d’autorisation, vous pouvez l’utiliser pour demander un jeton d’accès :

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Vous devez voir quelque chose de similaire à la réponse suivante :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Lorsque vous utilisez https://jwt.ms pour examiner le jeton d’accès qui a été retourné, vous devez voir quelque chose de similaire à l’exemple suivant :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur comment [configurer des jetons dans Azure AD B2C](configure-tokens.md)
