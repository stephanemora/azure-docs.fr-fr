---
title: Gérer la personnalisation des authentifications uniques et des jetons à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: En savoir plus sur la gestion de la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e72bd04bb41537546191b8ceb320c0722bd10146
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340289"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gérer la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C

Cet article explique comment gérer vos configurations de jetons, de session et d’authentification unique (SSO) à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>Configuration des revendications et de la durée de vie des jetons JWT

Pour modifier les paramètres de durée de vie de vos jetons, vous devez ajouter un élément [ClaimsProviders](claimsproviders.md) dans le fichier de partie de confiance de la stratégie que vous souhaitez affecter.  L’élément **ClaimsProviders** est un enfant de l’élément [TrustFrameworkPolicy](trustframeworkpolicy.md).

Insérez l’élément ClaimsProviders entre les éléments BasePolicy et RelyingParty du fichier de partie de confiance.

À l’intérieur, vous devez indiquer les informations qui modifient la durée de vie de votre jeton. Le code XML ressemble à cet exemple :

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Les valeurs suivantes sont définies dans l’exemple précédent :

- **Durée de vie des jetons d’accès** : la durée de vie d’un jeton accès est définie avec l’élément de métadonnées **token_lifetime_secs** . La valeur par défaut est de 3600 secondes (60 minutes).
- **Durée de vie des jetons d’ID** : la durée de vie d’un jeton d’ID est définie avec l’élément de métadonnées **id_token_lifetime_secs** . La valeur par défaut est de 3600 secondes (60 minutes).
- **Durée de vie des jetons d’actualisation** : la durée de vie d’un jeton d’actualisation est définie avec l’élément de métadonnées **refresh_token_lifetime_secs** . La valeur par défaut est de 1209600 secondes (14 jours).
- **Durée de vie de la fenêtre glissante du jeton d’actualisation** : si vous souhaitez définir une durée de vie pour la fenêtre glissante de votre jeton d’actualisation, définissez la valeur de l’élément de métadonnées **rolling_refresh_token_lifetime_secs** . La valeur par défaut est de 7776000 jours (90 jours). Si vous ne souhaitez pas appliquer une durée de vie à la fenêtre glissante, remplacez l’élément par `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Revendication de l’émetteur (iss)** : la revendication de l’émetteur (iss) est définie avec l’élément de métadonnées **IssuanceClaimPattern** . Les valeurs possibles sont `AuthorityAndTenantGuid` et `AuthorityWithTfp`.
- **Paramétrage de l’ID de stratégie représentant la revendication** : les options permettant de définir cette valeur sont `TFP` (Trust Framework Policy) et `ACR` (Authentication Context Reference). `TFP` est la valeur recommandée. Définissez **AuthenticationContextReferenceClaimPattern** avec la valeur de `None`.

    Dans l'élément **ClaimsSchema** , ajoutez l'élément suivant :

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Dans votre élément **OutputClaims** , ajoutez l'élément suivant :

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pour l’option ACR, supprimez l’élément **AuthenticationContextReferenceClaimPattern** .

- **Revendication de sujet (sub)** : cette option est définie par défaut sur ObjectID. Si vous souhaitez que cette option soit définie sur `Not Supported`, remplacez cette ligne :

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    par cette ligne :

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Les applications monopages utilisant le flux de code d’autorisation avec PKCE ont toujours une durée de vie de jeton d’actualisation de 24 heures. [En savoir plus sur les implications pour la sécurité des jetons d’actualisation dans le navigateur](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>Fournir des revendications facultatives à votre application

Les revendications de sortie du [profil technique de la stratégie de partie de confiance](relyingparty.md#technicalprofile) sont des valeurs qui sont retournées à une application. L’ajout de revendications de sortie émettra les revendications dans le jeton après un parcours utilisateur réussi et les enverra à l’application. Modifiez l’élément de profil technique dans la section de partie de confiance pour ajouter les revendications souhaitées en tant que revendication de sortie.

1. Ouvrez votre fichier de stratégie personnalisée. Par exemple SignUpOrSignin.xml.
1. Recherchez l’élément OutputClaims. Ajoutez l’élément OutputClaim que vous souhaitez inclure dans le jeton. 
1. Définissez les attributs de la revendication de sortie. 

L’exemple suivant ajoute la revendication `accountBalance`. La revendication accountBalance est envoyée à l’application sous forme de solde. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

L’élément OutputClaim contient les attributs suivants :

  - **ClaimTypeReferenceId**  : Identificateur d’un type de revendication déjà défini dans la section [ClaimsSchema](claimsschema.md) du fichier de stratégie ou d’un fichier de stratégie parent.
  - **PartnerClaimType**  : Vous permet de modifier le nom de la revendication dans le jeton. 
  - **DefaultValue**  : Valeur par défaut. Vous pouvez également définir la valeur par défaut sur un [programme de résolution de revendications](claim-resolver-overview.md), par exemple l’ID de locataire.
  - **AlwaysUseDefaultValue**  : force l’utilisation de la valeur par défaut.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur une [session Azure AD B2C](session-overview.md).
- Découvrez comment [configurer un comportement de session dans des stratégies personnalisées](session-behavior-custom-policy.md).
- Référence : [JwtIssuer](jwt-issuer-technical-profile.md).
