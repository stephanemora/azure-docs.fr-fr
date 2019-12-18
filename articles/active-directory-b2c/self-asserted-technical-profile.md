---
title: Définir un profil technique autodéclaré dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique autodéclaré dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950899"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique autodéclaré dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Toutes les interactions dans Azure Active Directory B2C (Azure AD B2C) où une saisie de l’utilisateur est attendue sont des profils techniques autodéclarés. Par exemple, une page d’inscription, une page de connexion ou une page de réinitialisation du mot de passe.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom qualifié complet de l’assembly de gestionnaire de protocole utilisé par Azure AD B2C pour l’autodéclaration : `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L’exemple suivant montre un profil technique autodéclaré pour l’inscription par e-mail :

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Revendications d’entrée

Dans un profil technique autodéclaré, vous pouvez utiliser les éléments **InputClaims** et **InputClaimsTransformations** pour prérenseigner la valeur des revendications qui apparaissent dans la page autodéclarée (revendications de sortie). Par exemple, dans la stratégie de profil de modification, le parcours utilisateur lit d’abord le profil utilisateur à partir du service d’annuaire Azure AD B2C, puis le profil technique autodéclaré définit les revendications d’entrée avec les données utilisateur stockées dans le profil utilisateur. Ces revendications sont collectées à partir du profil utilisateur, puis présentées à l’utilisateur qui peut ensuite modifier les données existantes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste des revendications à présenter pour recueillir des données à partir de l’utilisateur. Pour prérenseigner les revendications de sortie avec certaines valeurs, utilisez les revendications d’entrée décrites précédemment. L’élément peut également contenir une valeur par défaut. L’ordre des revendications dans **OutputClaims** détermine l’ordre dans lequel Azure AD B2C affiche les revendications à l’écran. L’attribut **DefaultValue** prend effet uniquement si la revendication n’a encore jamais été définie. En revanche, si elle a déjà été définie lors d’une étape d’orchestration précédente, même si l’utilisateur laisse la valeur vide la valeur par défaut ne prend pas effet. Pour forcer l’utilisation d’une valeur par défaut, affectez la valeur `true` à l’attribut **AlwaysUseDefaultValue**. Pour forcer l’utilisateur à fournir une valeur pour une revendication de sortie spécifique, affectez la valeur `true` à l’attribut **Required** de l’élément **OutputClaims**.

L’élément **ClaimType** dans la collection **OutputClaims** doit définir l’élément **UserInputType** sur n’importe quel type d’entrée d’utilisateur pris en charge par Azure AD B2C, tel que `TextBox`ou `DropdownSingleSelect`. Ou vous devez affecter une **DefaultValue** à l’élément **OutputClaim**.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

La revendication de sortie suivante est toujours définie sur `live.com` :

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Cas d’utilisation

Il existe quatre scénarios de revendications de sortie :

- **Collecte des revendications de sortie à partir de l’utilisateur** : quand vous avez besoin de recueillir des informations à partir de l’utilisateur, telles que sa date de naissance, vous devez ajouter la revendication à la collection **OutputClaims**. Les revendications présentées à l’utilisateur doivent spécifier le **UserInputType**, tel que `TextBox` ou `DropdownSingleSelect`. Si le profil technique autodéclaré contient un profil technique de validation qui génère la même revendication, Azure AD B2C ne présente pas la revendication à l’utilisateur. S’il n’y a aucune revendication de sortie à présenter à l’utilisateur, Azure AD B2C ignore le profil technique.
- **Définition d’une valeur par défaut dans une revendication de sortie** : sans recueillir de données de l’utilisateur ou retourner les données à partir du profil technique de validation. Le profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` définit la revendication **executed-SelfAsserted-Input** sur `true`.
- **Un profil technique de validation retourne les revendications de sortie** : votre profil technique peut appeler un profil technique de validation qui retourne certaines revendications. Vous souhaiterez peut-être faire remonter les revendications et les retourner aux étapes d’orchestration suivantes dans le parcours utilisateur. Par exemple, quand vous vous connectez avec un compte local, le profil technique autodéclaré nommé `SelfAsserted-LocalAccountSignin-Email` appelle le profil technique de validation nommé `login-NonInteractive`. Ce profil technique valide les informations d’identification utilisateur et retourne également le profil utilisateur. Par exemple « userPrincipalName », « displayName », « givenName » et « surName ».
- **Générer les revendications par le biais d’une transformation de revendication de sortie**

Dans l’exemple suivant, le profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` illustre l’utilisation de revendications de sortie et affecte la valeur `true` à **executed-SelfAsserted-Input**. Les revendications `objectId`, `authenticationSource` et `newUser` sont générées à partir du profil technique de validation `AAD-UserWriteUsingLogonEmail` et ne sont pas présentées à l’utilisateur.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Conserver les revendications

Si l’élément **PersistedClaims** est absent, le profil technique autodéclaré ne conserve pas les données dans Azure AD B2C. Au lieu de cela, un appel est effectué à un profil technique de validation qui est responsable de la persistance des données. Par exemple, la stratégie d’inscription utilise le profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` pour recueillir le nouveau profil utilisateur. Le profil technique `LocalAccountSignUpWithLogonEmail` appelle le profil technique de validation pour créer le compte dans Azure AD B2C.

## <a name="validation-technical-profiles"></a>Profils techniques de validation

Un profil technique de validation sert à valider toutes ou certaines des revendications de sortie du profil technique de référencement. Les revendications d’entrée du profil technique de validation doivent apparaître dans les revendications de sortie du profil technique autodéclaré. Le profil technique de validation valide l’entrée d’utilisateur et peut retourner une erreur à l’utilisateur.

Le profil technique de validation peut être n’importe quel profil technique dans la stratégie, tel qu’un profil technique [API REST](restful-technical-profile.md) ou [Azure Active Directory](active-directory-technical-profile.md). Dans l’exemple précédent, le profil technique `LocalAccountSignUpWithLogonEmail` valide le fait que le signinName n’existe pas dans l’annuaire. Si ce n’est pas le cas, le profil de validation technique crée un compte local et retourne les éléments objectId, authenticationSource et newUser. Le profil technique `SelfAsserted-LocalAccountSignin-Email` appelle le profil technique de validation `login-NonInteractive` pour valider les informations d’identification de l’utilisateur.

Vous pouvez également appeler un profil technique d’API REST avec votre logique métier, remplacer des revendications d’entrée ou enrichir les données utilisateur en fournissant une intégration approfondie à une application métier d’entreprise. Pour plus d’informations, consultez [Profil technique de Validation](validation-technical-profile.md)

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| setting.showContinueButton | Non | Affiche le bouton Continuer. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.showCancelButton | Non | Affiche le bouton Annuler. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.operatingMode | Non | Pour une page de connexion, cette propriété contrôle le comportement du champ username, tel que la validation de l’entrée et les messages d’erreur. Valeurs attendues : `Username` ou `Email`. |
| ContentDefinitionReferenceId | OUI | Identificateur de la [définition de contenu](contentdefinitions.md) associée à ce profil technique. |
| EnforceEmailVerification | Non | Pour l’inscription ou la modification du profil, applique la vérification par e-mail. Valeurs possibles : `true` (par défaut) ou `false`. |
| setting.showSignupLink | Non | Affiche le bouton d’inscription. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.retryLimit | Non | Contrôle le nombre de fois qu’un utilisateur peut essayer de fournir les données qui sont vérifiées par rapport à un profil technique de validation. Par exemple, un utilisateur tente de s’inscrire avec un compte qui existe déjà, et continue à essayer jusqu’à ce que la limite soit atteinte.
| SignUpTarget | Non | Identificateur d’échange de cible d’inscription. Quand l’utilisateur clique sur le bouton d’inscription, Azure AD B2C exécute l’identificateur d’échange spécifié. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** n’est pas utilisé.













