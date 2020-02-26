---
title: Définir un profil technique Azure AD dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique Azure Active Directory dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9f00bebfbab7b3726930e212893ae9dd2f5c17c8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193441"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique Azure Active Directory dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) prend en charge la gestion des utilisateurs Azure Active Directory. Cet article décrit les caractéristiques d’un profil technique permettant d’interagir avec un fournisseur de revendications qui prend en charge ce protocole normalisé.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom complet de l’assembly du gestionnaire de protocole`Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Tous les profils techniques Azure AD incluent le profil technique **AAD-Common**. Les profils techniques suivants ne spécifient pas le protocole parce que celui-ci est configuré dans le profil technique **AAD-Common** :

- **AAD-UserReadUsingAlternativeSecurityId** et **UserReadUsingAlternativeSecurityId-AAD-NoError** : rechercher un compte de réseau social dans le répertoire.
- **AAD-UserWriteUsingAlternativeSecurityId** : créer un compte de réseau social.
- **AAD-UserReadUsingEmailAddress** : rechercher un compte local dans le répertoire.
- **AAD-UserWriteUsingLogonEmail** : créer un compte local.
- **AAD-UserWritePasswordUsingObjectId** : mettre à jour un mot de passe de compte local.
- **AAD-UserWriteProfileUsingObjectId** : mettre à jour un profil utilisateur d’un compte local ou social.
- **AAD-UserReadUsingObjectId** : lire un profil utilisateur d’un compte local ou social.
- **AAD-UserWritePhoneNumberUsingObjectId** : écrire le numéro de téléphone d’authentification multifacteur d’un compte local ou social.

L’exemple suivant montre le profil technique **AAD-Common** :

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Revendications d’entrée

Les profils techniques suivantes incluent **InputClaims** pour les comptes de réseaux sociaux et locaux :

- Les profils techniques de compte de réseau social **AAD-UserReadUsingAlternativeSecurityId** et **AAD-UserWriteUsingAlternativeSecurityId** incluent la revendication **AlternativeSecurityId**. Cette revendication contient l’identificateur d’utilisateur du compte de réseau social.
- Les profils techniques de compte local **AAD-UserReadUsingEmailAddress** et **AAD-UserWriteUsingLogonEmail** incluent la revendication **email**. Cette revendication contient le nom de connexion du compte local.
- Les profils techniques (locaux et sociaux) unifiées **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId** et **AAD-UserWritePhoneNumberUsingObjectId** incluent la revendication **objectId**. Identificateur unique d’un compte.

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications retournée par le profil technique Azure AD. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans Azure Active Directory. Vous pouvez également inclure des revendications qui ne sont pas retournées par Azure Active Directory, pour autant que vous définissiez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

Par exemple, le profil technique **AAD-UserWriteUsingLogonEmail** crée un compte local et retourne les revendications suivantes :

- **objectId**, qui est l’identificateur du nouveau compte
- **newUser**, qui indique si l’utilisateur est nouveau
- **authenticationSource**, qui définit l’authentification sur `localAccountAuthentication`
- **userPrincipalName**, qui est le nom d’utilisateur principal du nouveau compte
- **signInNames.emailAddress**, qui est le nom de connexion du compte, similaire à la revendication d’entrée **email**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

L’élément **PersistedClaims** contient toutes les valeurs qui doivent être conservées par Azure AD, avec des informations de mappage possible entre un type de revendication déjà défini dans la section ClaimsSchema dans la stratégie et le nom d’attribut Azure AD.

Le profil technique **AAD-UserWriteUsingLogonEmail**, qui crée un nouveau compte local, conserve les revendications suivantes :

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Le nom de la revendication est le nom de l’attribut Azure AD, sauf si l’attribut **PartnerClaimType** est spécifié, qui contient le nom d’attribut Azure AD.

## <a name="requirements-of-an-operation"></a>Exigences d’une opération

- Il doit y avoir exactement un élément **InputClaim** dans le panier de revendications pour tous les profils techniques Azure AD.
- Si l’opération est `Write` ou `DeleteClaims`, il doit également apparaître dans un élément **PersistedClaims**.
- La valeur de la revendication **userPrincipalName** doit être au format `user@tenant.onmicrosoft.com`.
- La revendication **displayName** est obligatoire et ne peut pas être une chaîne vide.

## <a name="azure-ad-technical-provider-operations"></a>Opérations du fournisseur technique AD Azure

### <a name="read"></a>Lire

L’opération **Read** lit les données sur un seul compte d’utilisateur. Pour lire des données utilisateur, vous devez fournir une clé en tant que revendication d’entrée, telle que **objectId**, **userPrincipalName**, **signInNames** (de tout type, nom d’utilisateur, compte de courrier...) ou **alternativeSecurityId**.

Le profil technique suivant lit les données d’un compte d’utilisateur à l’aide de l’objectId de l’utilisateur :

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Write

L’opération **Write** crée ou met à jour un seul compte d’utilisateur. Pour écrire un compte d’utilisateur, vous devez fournir une clé en tant que revendication d’entrée, telle que **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou  **alternativeSecurityId**.

Le profil technique suivant crée un compte de réseau social :

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

L’opération **DeleteClaims** efface les informations d’une liste fournie de revendications. Pour supprimer des informations de revendications, vous devez fournir une clé en tant que revendication d’entrée, telles que **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou **alternativeSecurityId**.

Le profil technique suivant supprime les revendications :

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

L’opération **DeleteClaimsPrincipal** supprime un seul compte d’utilisateur de l’annuaire. Pour supprimer un compte d’utilisateur, vous devez fournir une clé en tant que revendication d’entrée, telle que **objectId**, **userPrincipalName**, **signInNames.emailAddress** ou  **alternativeSecurityId**.

Le profil technique suivant supprime un compte d’utilisateur du répertoire en utilisant le nom d’utilisateur principal :

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Le profil technique suivant supprime un compte d’utilisateur social en utilisant **alternativeSecurityId** :

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Opération | Oui | Opération à effectuer. Valeurs possibles : `Read`, `Write`, `DeleteClaims` ou `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Non | Génère une erreur si l’objet utilisateur n’existe pas dans le répertoire. Valeurs possibles : `true` ou `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Non | Si une erreur doit être déclenchée (voir la description de l’attribut RaiseErrorIfClaimsPrincipalDoesNotExist), spécifiez le message à afficher à l’utilisateur si l’objet utilisateur n’existe pas. La valeur peut être [localisée](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Non | Génère une erreur si l’objet utilisateur existe déjà. Valeurs possibles : `true` ou `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Non | Si une erreur doit être déclenchée (voir la description de l’attribut RaiseErrorIfClaimsPrincipalAlreadyExists), spécifiez le message à afficher à l’utilisateur si l’objet utilisateur existe déjà. La valeur peut être [localisée](localization.md).|
| ApplicationObjectId | Non | Identificateur d’objet d’application pour les attributs d’extension. Valeur : ObjectId d'une application. Pour plus d’informations, voir [Utiliser des attributs personnalisés dans une stratégie personnalisée de modification de profil](custom-policy-custom-attributes.md). |
| ClientId | Non | Identificateur client pour accéder au locataire en tant que tiers. Pour plus d’informations, voir [Utiliser des attributs personnalisés dans une stratégie personnalisée de modification de profil](custom-policy-custom-attributes.md). |
| IncludeClaimResolvingInClaimsHandling  | Non | Pour les revendications d’entrée et de sortie, spécifie si la [résolution des revendications](claim-resolver-overview.md) est incluse dans le profil technique. Valeurs possibles : `true` ou `false` (par défaut). Si vous voulez utiliser un résolveur de revendications dans le profil technique, définissez ceci sur `true`. |














