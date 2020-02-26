---
title: Définir un profil technique OpenID Connect dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique OpenID Connect dans une stratégie personnalisée d'Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fde27c468b6b5285e9e98f10a5fd04ee53c3c775
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198001"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique OpenID Connect dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) prend en charge le fournisseur d’identité du protocole [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/). OpenID Connect 1.0 définit une couche d’identité par dessus OAuth 2.0, et est à la pointe des protocoles d’authentification modernes. Un profil technique OpenID Connect vous permet d'opérer une fédération avec un fournisseur d'identité reposant sur OpenID Connect, comme Azure AD. Fédérer avec un fournisseur d’identité permet aux utilisateurs de se connecter avec leurs identités existantes de réseaux sociaux ou d’entreprise.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `OpenIdConnect`. Par exemple, le protocole pour le profil technique **MSA-OIDC** est `OpenIdConnect` :

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Revendications d’entrée

Les éléments **InputClaims** et **InputClaimsTransformations** ne sont pas obligatoires. Vous pouvez cependant envoyer des paramètres supplémentaires à votre fournisseur d’identité. L’exemple suivant ajoute le paramètre de chaîne de requête **domain_hint** avec la valeur `contoso.com` à la demande d’autorisation.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications renvoyée par le fournisseur d'identité OpenID Connect. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d'identité. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre les revendications retournées par le fournisseur d’identité Compte Microsoft :

- La revendication **sub** mappée à la revendication **issuerUserId**.
- La revendication **name** mappée à la revendication **displayName**.
- La revendication **e-mail** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité :

- La revendication **identityProvider** contenant le nom du fournisseur d’identité.
- La revendication **authenticationSource** avec la valeur par défaut **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | Oui | Identificateur d’application du fournisseur d’identité. |
| IdTokenAudience | Non | Audience du jeton id_token. Si la valeur est spécifiée, Azure AD B2C vérifie si le jeton figure dans une revendication retournée par le fournisseur d’identité, et est identique à celui spécifié. |
| METADATA | Oui | URL qui pointe vers un document de configuration JSON mis en forme conformément à la spécification OpenID Connect Discovery, qui est un point de terminaison de configuration openid bien connu. |
| ProviderName | Non | Nom du fournisseur d'identité. |
| response_types | Non | Type de réponse conformément à la spécification OpenID Connect Core 1.0. Valeurs possibles : `id_token`, `code` ou `token`. |
| response_mode | Non | Méthode que le fournisseur d’identité utilise pour renvoyer le résultat à Azure AD B2C. Valeurs possibles : `query`, `form_post` (par défaut) ou `fragment`. |
| scope | Non | Étendue de la requête définie conformément à la spécification OpenID Connect Core 1.0. Par exemple, `openid`, `profile` ou `email`. |
| HttpBinding | Non | Liaison HTTP attendue aux points de terminaison de jeton d’accès et de jeton de revendications. Valeurs possibles : `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Non | Clé utilisable pour se connecter à chacun des locataires en cas d’utilisation d’un fournisseur d’identité mutualisé tel qu’Azure Active Directory. |
| UsePolicyInRedirectUri | Non | Indique s’il faut utiliser une stratégie lors de la construction de l’URI de redirection. Lorsque vous configurez votre application dans le fournisseur d’identité, vous devez spécifier l’URI de redirection. L’URI de redirection pointe vers Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Si vous spécifiez `false`, vous devez ajouter un URI de redirection pour chaque stratégie que vous utilisez. Par exemple : `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Non | Indique si une demande adressée à un service externe doit être marquée comme un échec si le code d’état HTTP s’inscrit dans la plage 5xx. Par défaut, il s’agit de `false`. |
| DiscoverMetadataByTokenIssuer | Non | Indique si les métadonnées OIDC doivent être découvertes à l’aide de l’émetteur dans le jeton JSON Web Token. |
| IncludeClaimResolvingInClaimsHandling  | Non | Pour les revendications d’entrée et de sortie, spécifie si la [résolution des revendications](claim-resolver-overview.md) est incluse dans le profil technique. Valeurs possibles : `true` ou `false` (par défaut). Si vous voulez utiliser un résolveur de revendications dans le profil technique, définissez ceci sur `true`. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_secret | Oui | Clé secrète client de l’application du fournisseur d’identité. La clé de chiffrement est requise uniquement si les métadonnées **response_types** sont définies sur `code`. Dans ce cas, Azure AD B2C émet un autre appel pour échanger le code d’autorisation pour un jeton d’accès. Si les métadonnées sont définies sur `id_token`, vous pouvez omettre la clé de chiffrement.  |

## <a name="redirect-uri"></a>URI de redirection

Lorsque vous configurez l’URI de redirection de votre fournisseur d’identité, entrez `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Veillez à remplacer `{your-tenant-name}` par le nom de votre locataire. L’URI de redirection doit être en minuscules.

Exemples :

- [Ajouter Compte Microsoft (MSA) comme fournisseur d’identité utilisant des stratégies personnalisées](identity-provider-microsoft-account-custom.md)
- [Se connecter à l’aide de comptes Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Autoriser la connexion d’utilisateurs à un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées](identity-provider-azure-ad-multi-tenant-custom.md)
