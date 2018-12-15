---
title: Définir un profil technique OpenId Connect dans une stratégie personnalisée dans Azure Active Directory B2C | Microsoft Docs
description: Définir un profil technique OpenId Connect dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a20b8df1f0d9cddbde4c4886e11967bc8c04160
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842406"
---
# <a name="define-a-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique OpenId Connect dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C assure le support pour le fournisseur d’identité de protocole [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/). OpenID Connect 1.0 définit une couche d’identité par dessus OAuth 2.0, et est à la pointe des protocoles d’authentification modernes.  Avec un profil technique OpenId Connect, vous pouvez fédérer avec un fournisseur d’identité basé sur OpenId Connect, tel qu’Azure AD, ce qui permet aux utilisateurs de se connecter avec leurs identités sociales ou d’entreprise existantes.

## <a name="protocol"></a>Protocole

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

L’élément **OutputClaims** contient une liste de revendications retournée par le fournisseur d'identité OpenId Connect. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d'identité. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre les revendications retournées par le fournisseur d’identité Compte Microsoft :

- La revendication **sub** mappée à la revendication **socialIdpUserId**.
- La revendication **name** mappée à la revendication **displayName**.
- La revendication **e-mail** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité :

- La revendication **identityProvider** contenant le nom du fournisseur d’identité.
- La revendication **authenticationSource** avec la valeur par défaut **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | Oui | Identificateur d’application du fournisseur d’identité. |
| IdTokenAudience | Non  | Audience du jeton id_token. Si la valeur est spécifiée, Azure AD B2C vérifie si le jeton figure dans une revendication retournée par le fournisseur d’identité, et est identique à celui spécifié. |
| METADATA | Oui | URL qui pointe vers un document de configuration JSON mis en forme conformément à la spécification OpenID Connect Discovery, qui est un point de terminaison de configuration openid bien connu. |
| ProviderName | Non  | Nom du fournisseur d'identité. |
| response_types | Non  | Type de réponse conformément à la spécification OpenID Connect Core 1.0. Valeurs possibles : `id_token`, `code` ou `token`. |
| response_mode | Non  | Méthode que le fournisseur d’identité utilise pour renvoyer le résultat à Azure AD B2C. Valeurs possibles : `query`, `form_post` (par défaut) ou `fragment`. |
| scope | Non  | Étendue de la demande d’accès définie conformément à la spécification OpenID Connect Core 1.0. Par exemple, `openid`, `profile` ou `email`. |
| HttpBinding | Non  | Liaison HTTP attendue aux points de terminaison de jeton d’accès et de jeton de revendications. Valeurs possibles : `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Non  | Clé utilisable pour se connecter à chacun des locataires en cas d’utilisation d’un fournisseur d’identité mutualisé tel qu’Azure Active Directory. |
| UsePolicyInRedirectUri | Non  | Indique s’il faut utiliser une stratégie lors de la construction de l’URI de redirection. Lorsque vous configurez votre application dans le fournisseur d’identité, vous devez spécifier l’URI de redirection. L’URI de redirection pointe vers Azure AD B2C, `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com peut être remplacé par votre-nom-locataire.b2clogin.com).  Si vous spécifiez `false`, vous devez ajouter un URI de redirection pour chaque stratégie que vous utilisez. Par exemple : `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Non  | Indique si une demande adressée à un service externe doit être marquée comme un échec si le code d’état HTTP s’inscrit dans la plage 5xx. Par défaut, il s’agit de `false`. |
| DiscoverMetadataByTokenIssuer | Non  | Indique si les métadonnées OIDC doivent être découvertes à l’aide de l’émetteur dans le jeton JSON Web Token. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_secret | Oui | Clé secrète client de l’application du fournisseur d’identité. La clé de chiffrement est requise uniquement si les métadonnées **response_types** sont définies sur `code`. Dans ce cas, Azure AD B2C émet un autre appel pour échanger le code d’autorisation pour un jeton d’accès. Si les métadonnées sont définies sur `id_token`, vous pouvez omettre la clé de chiffrement.  |  

## <a name="redirect-uri"></a>URI de redirection
 
Lorsque vous configurez l’URI de redirection de votre fournisseur d’identité, entrez `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Veillez à remplacer **tenant** par le nom de votre locataire (par exemple, contosob2c.onmicrosoft.com) ou l’ID du locataire. L’URI de redirection doit être en minuscules.

Si vous utilisez le domaine **b2clogin.com** à la place de **login.microsoftonline.com**, veillez à utiliser b2clogin.com au lieu de login.microsoftonline.com.

Exemples :

- [Ajouter Compte Microsoft (MSA) comme fournisseur d’identité utilisant des stratégies personnalisées](active-directory-b2c-custom-setup-msa-idp.md)
- [Se connecter à l’aide de comptes Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Autoriser la connexion d’utilisateurs à un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées](active-directory-b2c-setup-commonaad-custom.md)

 














