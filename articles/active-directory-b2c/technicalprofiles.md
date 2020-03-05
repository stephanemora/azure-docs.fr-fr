---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Spécifiez l’élément TechnicalProfiles d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4dbe5a62e69f4c30d55fa1318ca79c06640a10f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186741"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un élément **TechnicalProfiles** contient un ensemble de profils techniques pris en charge par le fournisseur de revendication. Chaque fournisseur de revendications doit avoir un ou plusieurs profils techniques déterminant les points de terminaison et les protocoles nécessaires pour communiquer avec lui. Un fournisseur de revendications peut avoir plusieurs profils techniques.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L’élément **TechnicalProfile** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
|---------|---------|---------|
| Id | Oui | Identificateur unique du profil technique. Le profil technique peut être référencé à l’aide de cet identificateur à partir d’autres éléments dans le fichier de stratégie. Par exemple, **OrchestrationSteps** et **ValidationTechnicalProfile**. |

L’élément **TechnicalProfile** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Nom de domaine pour le profil technique. Par exemple, si votre profil technique Spécifie le fournisseur d’identité Facebook, le nom de domaine est Facebook.com. |
| DisplayName | 1:1 | Nom du profil technique qui peut être affiché aux utilisateurs. |
| Description | 0:1 | Description du profil technique qui peut être affichée aux utilisateurs. |
| Protocol | 0:1 | Protocole utilisé pour la communication avec l’autre partie. |
| Métadonnées | 0:1 | Collection de paires clé/valeur utilisées par le protocole pour communiquer avec le point de terminaison durant une transaction. |
| InputTokenFormat | 0:1 | Format du jeton d’entrée. Valeurs possibles : `JSON`, `JWT`, `SAML11` ou `SAML2`. La valeur `JWT` représente un JSON Web Token conforme à la spécification IETF. La valeur `SAML11` représente un jeton de sécurité SAML 1.1 conforme à la spécification OASIS.  La valeur `SAML2` représente un jeton de sécurité SAML 2.0 conforme à la spécification OASIS. |
| OutputTokenFormat | 0:1 | Format du jeton de sortie. Valeurs possibles : `JSON`, `JWT`, `SAML11` ou `SAML2`. |
| CryptographicKeys | 0:1 | Liste de clés de chiffrement utilisées dans le profil technique. |
| InputClaimsTransformations | 0:1 | Liste des références précédemment définies aux transformations de revendications qui doivent être exécutées avant l’envoi de toute revendication au fournisseur de revendications ou à la partie de confiance. |
| InputClaims | 0:1 | Liste des références précédemment définies aux types de revendications qui sont pris en tant qu’entrée dans le profil technique. |
| PersistedClaims | 0:1 | Liste des références précédemment définies aux types de revendications qui sont conservés par le fournisseur de revendications, qui a trait au profil technique. |
| DisplayClaims | 0:1 | Liste des références précédemment définies aux types de revendications qui sont présentés par le fournisseur de revendications, qui a trait au [profil technique autodéclaré](self-asserted-technical-profile.md). La fonctionnalité DisplayClaims est actuellement en **préversion**. |
| OutputClaims | 0:1 | Liste des références précédemment définies aux types de revendications qui sont pris en tant que sortie dans le profil technique. |
| OutputClaimsTransformations | 0:1 | Liste des références précédemment définies aux transformations de revendications qui doivent être exécutées après la réception des revendications provenant du fournisseur de revendications. |
| ValidationTechnicalProfiles | 0:n | Liste des références à d’autres profils techniques que le profil technique utilise à des fins de validation. Pour plus d’informations, voir [Profil technique de validation](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Contrôle la production du nom du sujet dans les jetons où le nom du sujet est spécifié séparément à partir de revendications. Par exemple, OAuth ou SAML.  |
| IncludeInSso | 0:1 |  Si l’utilisation de ce profil technique doit appliquer le comportement de l’authentification unique (SSO) pour la session, ou si une interaction explicite est exigée à la place. Cet élément est valide uniquement dans les profils SelfAsserted utilisés dans un profil technique de validation. Valeurs possibles : `true` (par défaut) ou `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identificateur d’un profil technique à partir duquel vous souhaitez que toutes les revendications d’entrée et de sortie soient ajoutées à ce profil technique. Le profil technique référencé doit être défini dans le même fichier de stratégie. |
| IncludeTechnicalProfile |0:1 | Identificateur d’un profil technique à partir duquel vous souhaitez que toutes les sonnées soient ajoutées à ce profil technique. Le profil technique référencé doit exister dans le même fichier de stratégie. |
| UseTechnicalProfileForSessionManagement | 0:1 | Autre profil technique à utiliser pour la gestion de session. |
|EnabledForUserJourneys| 0:1 |Contrôle si le profil technique est exécuté dans un parcours utilisateur.  |

## <a name="protocol"></a>Protocol

L’élément **Protocol** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Nom | Oui | Nom d’un protocole valide pris en charge par Azure AD B2C et utilisé dans le cadre du profil technique. Valeurs possibles : `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary` ou `None`. |
| Handler | Non | Lorsque le nom de protocole est défini sur `Proprietary`, spécifiez le nom complet de l’assembly qu’Azure AD B2C utilise pour déterminer le gestionnaire de protocole. |

## <a name="metadata"></a>Métadonnées

Un élément **Metadata** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Élément | 0:n | Métadonnées relatives au profil technique. Chaque type de profil technique a un ensemble différent d’éléments de métadonnées. Pour plus d’informations, voir la section consacrée aux types de profils techniques. |

### <a name="item"></a>Élément

L’élément **Item** de l’élément **Metadata** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Clé | Oui | Clé de métadonnées. Pour obtenir la liste des éléments de métadonnées, voir chaque type de profil technique. |

## <a name="cryptographickeys"></a>CryptographicKeys

L’élément **CryptographicKeys** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Clé | 1:n | Clé de chiffrement utilisée dans ce profil technique. |

### <a name="key"></a>Clé

L’élément **Key** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Id | Non | Identificateur unique d’une paire de clés spécifique référencé à partir d’autres éléments dans le fichier de stratégie. |
| StorageReferenceId | Oui | Identificateur d’un conteneur de clé de stockage référencé à partir d’autres éléments dans le fichier de stratégie. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

L’élément **InputClaimsTransformations** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | Identificateur d’une transformation de revendications qui doit être exécutée avant l’envoi de toute revendication au fournisseur de revendications ou à la partie de confiance. Une transformation de revendications peut être utilisée pour modifier des revendications ClaimsSchema existantes ou en générer de nouvelles. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

L’élément **InputClaimsTransformation** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ReferenceId | Oui | Identificateur d’une transformation de revendications déjà défini dans le fichier de stratégie ou un fichier de stratégie parent. |

## <a name="inputclaims"></a>InputClaims

L’élément **InputClaims** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Type de revendication d’entrée attendu. |

### <a name="inputclaim"></a>InputClaim

L’élément **InputClaim** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Oui | Identificateur d’un type de revendication déjà défini dans la section ClaimsSchema du fichier de stratégie ou d’un fichier de stratégie parent. |
| DefaultValue | Non | Valeur par défaut à utiliser pour créer une revendication si la revendication indiquée par l’identificateur ClaimTypeReferenceId n’existe pas, de sorte que le profil technique puisse utiliser la revendication obtenue comme InputClaim. |
| PartnerClaimType | Non | Identificateur du type de revendication du partenaire externe auquel le type de revendication de stratégie spécifié mappe. Si l’attribut PartnerClaimType n’est pas spécifié, le type de revendication de stratégie spécifié est mappé au type de revendication de partenaire du même nom. Utilisez cette propriété lorsque le nom de votre type de revendication diffère de celui de l’autre partie. Par exemple, le nom de la première revendication est « givenName », tandis que le partenaire utilise une revendication nommée « first_name ». |

## <a name="displayclaims"></a>DisplayClaims

L’élément **DisplayClaims** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Type de revendication d’entrée attendu. |

La fonctionnalité DisplayClaims est actuellement en **préversion**.

### <a name="displayclaim"></a>DisplayClaim

L’élément **DisplayClaim** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Non | Identificateur d’un type de revendication déjà défini dans la section ClaimsSchema du fichier de stratégie ou d’un fichier de stratégie parent. |
| DisplayControlReferenceId | Non | Identificateur d’un [contrôle d’affichage](display-controls.md) déjà défini dans la section ClaimsSchema du fichier de stratégie ou d’un fichier de stratégie parent. |
| Obligatoire | Non | Indique si la revendication d’affichage est requise. |

L’élément **DisplayClaim** nécessite que vous spécifiiez un `ClaimTypeReferenceId` ou un `DisplayControlReferenceId`.

### <a name="persistedclaims"></a>PersistedClaims

L’élément **PersistedClaims** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Type de revendication à conserver. |

### <a name="persistedclaim"></a>PersistedClaim

L’élément **PersistedClaim** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Oui | Identificateur d’un type de revendication déjà défini dans la section ClaimsSchema du fichier de stratégie ou d’un fichier de stratégie parent. |
| DefaultValue | Non | Valeur par défaut à utiliser pour créer une revendication si la revendication indiquée par l’identificateur ClaimTypeReferenceId n’existe pas, de sorte que le profil technique puisse utiliser la revendication obtenue comme InputClaim. |
| PartnerClaimType | Non | Identificateur du type de revendication du partenaire externe auquel le type de revendication de stratégie spécifié mappe. Si l’attribut PartnerClaimType n’est pas spécifié, le type de revendication de stratégie spécifié est mappé au type de revendication de partenaire du même nom. Utilisez cette propriété lorsque le nom de votre type de revendication diffère de celui de l’autre partie. Par exemple, le nom de la première revendication est « givenName », tandis que le partenaire utilise une revendication nommée « first_name ». |

## <a name="outputclaims"></a>OutputClaims

L’élément **OutputClaims** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Type de revendication de sortie attendu. |

### <a name="outputclaim"></a>OutputClaim

L’élément **OutputClaim** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Oui | Identificateur d’un type de revendication déjà défini dans la section ClaimsSchema du fichier de stratégie ou d’un fichier de stratégie parent. |
| DefaultValue | Non | Valeur par défaut à utiliser pour créer une revendication si la revendication indiquée par l’identificateur ClaimTypeReferenceId n’existe pas, de sorte que le profil technique puisse utiliser la revendication obtenue comme InputClaim. |
|AlwaysUseDefaultValue |Non |Forcer l’utilisation de la valeur par défaut.  |
| PartnerClaimType | Non | Identificateur du type de revendication du partenaire externe auquel le type de revendication de stratégie spécifié mappe. Si l’attribut PartnerClaimType n’est pas spécifié, le type de revendication de stratégie spécifié est mappé au type de revendication de partenaire du même nom. Utilisez cette propriété lorsque le nom de votre type de revendication diffère de celui de l’autre partie. Par exemple, le nom de la première revendication est « givenName », tandis que le partenaire utilise une revendication nommée « first_name ». |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

L’élément **OutputClaimsTransformations** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Identificateurs de transformations de revendications qui doivent être exécutées avant l’envoi de toute revendication au fournisseur de revendications ou à la partie de confiance. Une transformation de revendications peut être utilisée pour modifier des revendications ClaimsSchema existantes ou en générer de nouvelles. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

L’élément **OutputClaimsTransformation** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ReferenceId | Oui | Identificateur d’une transformation de revendications déjà défini dans le fichier de stratégie ou un fichier de stratégie parent. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

L’élément **ValidationTechnicalProfiles** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Identificateur des profils techniques utilisés pour valider la totalité ou certaines des revendications de sortie du profil technique de référencement. Toutes les revendications d’entrée du profil technique référencé doivent apparaître dans les revendications de sortie du profil technique de référencement. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

L’élément **ValidationTechnicalProfile** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ReferenceId | Oui | Identificateur d’un profil technique déjà défini dans le fichier de stratégie ou dans un fichier de stratégie parent. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

L’élément **SubjectNamingInfo** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClaimType | Oui | Identificateur d’un type de revendication déjà défini dans la section ClaimsSchema du fichier de stratégie. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

L’élément **IncludeTechnicalProfile** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ReferenceId | Oui | Identificateur d’un profil technique déjà défini dans le fichier de stratégie ou dans un fichier de stratégie parent. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

L’élément **UseTechnicalProfileForSessionManagement** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ReferenceId | Oui | Identificateur d’un profil technique déjà défini dans le fichier de stratégie ou dans un fichier de stratégie parent. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

L’élément **ClaimsProviderSelections** dans un parcours utilisateur définit la liste et l’ordre des options de sélection de fournisseur de revendications. Avec l’élément **EnabledForUserJourneys**, vous filtrez le fournisseur de revendications disponible pour l’utilisateur. L’élément **EnabledForUserJourneys** contient l’une des valeurs suivantes :

- **Always**, exécuter le profil technique.
- **Never**, ignorer le profil technique.
- **OnClaimsExistence**, exécuter seulement s’il existe une certaine revendication spécifiée dans le profil technique.
- **OnItemExistenceInStringCollectionClaim**, exécuter uniquement s’il existe un élément dans une revendication de collection de chaîne.
- **OnItemAbsenceInStringCollectionClaim**, exécuter uniquement s’il n’existe pas d’élément dans une revendication de collection de chaîne.

L’utilisation de **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim**, requiert que vous fournissez les métadonnées suivantes : **ClaimTypeOnWhichToEnable** spécifie le type de la revendication à évaluer, **ClaimValueOnWhichToEnable** spécifie la valeur à comparer.

Le profil technique suivant est exécuté uniquement si la collection de chaînes **identityProviders** contient la valeur de `facebook.com` :

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
