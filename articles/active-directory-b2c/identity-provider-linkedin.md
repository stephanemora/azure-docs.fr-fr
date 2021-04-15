---
title: Configurer l’inscription et la connexion avec un compte LinkedIn
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes LinkedIn dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51a9635d42b07eb27b05312d292ca890c7963b4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028228"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte LinkedIn à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Création d’une application LinkedIn

Pour autoriser la connexion des utilisateurs avec un compte LinkedIn dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application sur le [site web Développeurs LinkedIn](https://www.developer.linkedin.com/). Pour plus d’informations, consultez [Flux du code d’autorisation](/linkedin/shared/authentication/authorization-code-flow). Si vous n’avez pas déjà de compte LinkedIn, vous pouvez vous inscrire à l’adresse [https://www.linkedin.com/](https://www.linkedin.com/).

1. Connectez-vous au [site web Développeurs LinkedIn](https://www.developer.linkedin.com/) à l’aide des informations d’identification de votre compte LinkedIn.
1. Sélectionnez **Mes applications**, puis cliquez sur **Créer une application**.
1. Renseignez **Nom de l’application**, **Page LinkedIn**, **URL de la politique de confidentialité** et **Logo de l’application**.
1. Acceptez les **conditions d’utilisation de l’API** LinkedIn et cliquez sur **Créer une application**.
1. Sélectionnez l’onglet **Auth**. Sous **Clés d’authentification**, copiez les valeurs pour **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer LinkedIn en tant que fournisseur d’identité dans votre locataire. **Client Secret** est une information d’identification de sécurité importante.
1. Sélectionnez l’icône Crayon de modification en regard du champ **URL de redirection autorisées pour votre application**, puis sélectionnez **Ajouter une URL de redirection**. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C. Sélectionnez **Update**.
1. Par défaut, votre application LinkedIn n’est pas approuvée pour les étendues liées à la connexion. Pour demander une révision, sélectionnez l’onglet **Produits**, puis sélectionnez **Se connecter avec LinkedIn**. Une fois la révision terminée, les étendues requises sont ajoutées à votre application.
   > [!NOTE]
   > Vous pouvez afficher les étendues actuellement autorisées pour votre application sous l’onglet **Auth** dans la section **Étendues OAuth 2.0**.

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Configurer LinkedIn en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **LinkedIn**.
1. Saisissez un **Nom**. Par exemple, *LinkedIn*.
1. Dans **ID client**, entrez l’ID client de l’application LinkedIn que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité LinkedIn à un flux d’utilisateur 

À ce stade, le fournisseur d’identité LinkedIn a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Pour ajouter le fournisseur d’identité LinkedIn à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité LinkedIn.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **LinkedIn**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux utilisateur**.
1. Sur la page d'inscription ou de connexion, sélectionnez **LinkedIn** pour vous connecter avec un compte LinkedIn.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `LinkedInSecret`. Le préfixe *B2C_1A_* est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez notée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Configurer LinkedIn en tant que fournisseur d’identité

Si vous permettez aux utilisateurs de se connecter à l’aide d’un compte LinkedIn, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez un compte LinkedIn en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *SocialAndLocalAccounts/ **TrustFrameworkExtensions.xml*** dans votre éditeur. Ce fichier se trouve dans le [Pack de démarrage de stratégie personnalisée][starter-pack] que vous avez téléchargé dans le cadre de l’une des conditions préalables.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Remplacez la valeur de l’élément **client_id** par l’ID client de l’application LinkedIn que vous avez noté précédemment.
1. Enregistrez le fichier .

### <a name="add-the-claims-transformations"></a>Ajouter les transformations de revendications

Le profil technique LinkedIn nécessite l’ajout des transformations de revendications **ExtractGivenNameFromLinkedInResponse** et **ExtractSurNameFromLinkedInResponse** à la liste ClaimsTransformations. Si vous n’avez pas d’élément **ClaimsTransformations** défini dans votre fichier, ajoutez les éléments XML parents, comme indiqué ci-dessous. Les transformations de revendications nécessitent en outre la définition d’un nouveau type de revendication nommé **nullStringClaim**.

Ajoutez l’élément **BuildingBlocks** près du haut du fichier *TrustFrameworkExtensions.xml*. Consultez le fichier *TrustframeworkBase.xml* pour obtenir un exemple.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Sur la page d'inscription ou de connexion, sélectionnez **LinkedIn** pour vous connecter avec un compte LinkedIn.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="migration-from-v10-to-v20"></a>Migration de v1.0 vers v2.0

Dernièrement, LinkedIn a [mis à jour ses API v1.0 vers v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Pour migrer votre configuration existante vers la nouvelle, servez-vous des informations contenues dans les sections suivantes pour mettre à jour les éléments du profil technique.

### <a name="replace-items-in-the-metadata"></a>Remplacer des éléments dans Metadata

Dans l’élément **Metadata** existant de **TechnicalProfile**, remplacez les éléments **Item** suivants :

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Par :

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Ajouter des éléments à Metadata

Dans l’élément **Metadata** de **TechnicalProfile**, ajoutez les éléments **Item** suivants :

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Mettre à jour OutputClaims

Dans l’élément **OutputClaims** de **TechnicalProfile**, remplacez les éléments **OutputClaim** suivants :

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Par :

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Ajouter de nouveaux éléments OutputClaimsTransformation

Dans l’élément **OutputClaimsTransformations** de **TechnicalProfile**, ajoutez les éléments **OutputClaimsTransformation** suivants :

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Définir les nouvelles transformations de revendications et le nouveau type de revendication

À la dernière étape, vous avez ajouté de nouvelles transformations de revendications qui doivent être définies. Pour définir les transformations de revendications, ajoutez-les à la liste **ClaimsTransformations**. Si vous n’avez pas d’élément **ClaimsTransformations** défini dans votre fichier, ajoutez les éléments XML parents, comme indiqué ci-dessous. Les transformations de revendications nécessitent en outre la définition d’un nouveau type de revendication nommé **nullStringClaim**.

L’élément **BuildingBlocks** doit être ajouté vers le haut du fichier. Examinez le fichier *TrustframeworkBase.xml* en guise d’exemple.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Obtenir une adresse e-mail

Dans le cadre de la migration de LinkedIn v1.0 vers v2.0, un appel supplémentaire à une autre API est nécessaire pour obtenir l’adresse e-mail. Si vous avez besoin d’obtenir l’adresse e-mail pendant l’inscription, procédez comme suit :

1. Effectuez les étapes ci-dessus pour permettre à Azure AD B2C de se fédérer avec LinkedIn et permettre à l’utilisateur de se connecter. Dans le cadre de la fédération, Azure AD B2C reçoit le jeton d’accès pour LinkedIn.
1. Enregistrez le jeton d’accès LinkedIn dans une revendication. [Les instructions se trouvent ici](idp-pass-through-user-flow.md).
1. Ajoutez le fournisseur de revendications ci-dessous qui effectue la demande à l’API `/emailAddress` de LinkedIn. Pour autoriser cette demande, vous avez besoin du jeton d’accès LinkedIn.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ajoutez l’étape d’orchestration ci-dessous à votre parcours utilisateur, de sorte que le fournisseur de revendications d’API se déclenche quand un utilisateur se connecte avec LinkedIn. Veillez à mettre à jour le numéro `Order` de façon appropriée. Ajoutez cette étape de suite après celle d’orchestration qui déclenche le profil technique LinkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

L’obtention de l’adresse e-mail auprès de LinkedIn pendant l’inscription est facultative. Si vous choisissez de ne pas obtenir l’adresse e-mail auprès de LinkedIn mais que vous en demandez une pendant l’inscription, l’utilisateur est tenu d’entrer manuellement l’adresse e-mail et de la valider.

Pour obtenir un exemple complet de stratégie utilisant le fournisseur d’identité LinkedIn, consultez le [pack de démarrage de stratégie personnalisée](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migration de v1.0 vers v2.0

Dernièrement, LinkedIn a [mis à jour ses API v1.0 vers v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Dans le cadre de la migration, Azure AD B2C n’est en mesure d’obtenir le nom complet de l’utilisateur LinkedIn lors du processus d'inscription. Si une adresse e-mail figure parmi les attributs collectés au moment de l'inscription, l’utilisateur doit manuellement entrer l’adresse e-mail et la valider.

::: zone-end