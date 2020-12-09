---
title: Configurer la connexion avec un compte LinkedIn en utilisant des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Configurez la connexion avec un compte LinkedIn dans Azure Active Directory B2C à l’aide de stratégies personnalisées.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 084a8e247ab8bb3e6e6c75cf8cfe8e204384f549
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345148"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte LinkedIn à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment activer la connexion d’utilisateurs à partir d’un compte LinkedIn à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Conditions préalables requises

- Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).
- Compte LinkedIn- Si vous n’en avez pas encore, [créez un compte](https://www.linkedin.com/start/join).
- Page LinkedIn - Vous avez besoin d’une [page LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) à associer à l’application LinkedIn que vous créez dans la section suivante.

## <a name="create-an-application"></a>Créer une application

Pour utiliser LinkedIn comme fournisseur d’identité dans Azure AD B2C, vous devez créer une application LinkedIn.

### <a name="create-app"></a>Créer une application

1. Connectez-vous au site web [LinkedIn application management](https://www.linkedin.com/secure/developer?newapp=) (Gestion des applications LinkedIn) à l’aide des informations d’identification de votre compte LinkedIn.
1. Sélectionnez **Créer une application**.
1. Entrez un **nom d’application**.
1. Entrez un nom de **Société** correspondant à un nom de page LinkedIn. Créez une page LinkedIn si vous n’en avez pas déjà.
1. (Facultatif) Entrez une **URL de la politique de confidentialité**. Il doit s’agir d’une URL valide, mais pas nécessairement un point de terminaison accessible.
1. Entrez une **Adresse e-mail professionnelle**.
1. Chargez une image du **logo de l’application**. L’image du logo doit être carrée et ses dimensions doivent être d’au moins 100 x 100 pixels.
1. Conservez les paramètres par défaut dans la section **Produits**.
1. Passez en revue les informations présentées dans les **Termes et conditions**. Si vous acceptez les conditions, cochez la case.
1. Sélectionnez **Créer une application**.

### <a name="configure-auth"></a>Configurer l’authentification

1. Sélectionnez l’onglet **Auth**.
1. Notez l’**ID client**.
1. Révélez et notez la **clé secrète client**.
1. Sous **Paramètres OAuth 2.0**, ajoutez l’**URL de redirection** suivante. Remplacez `your-tenant` par le nom de votre locataire. Utilisez uniquement des **minuscules** pour le nom du locataire, même s’il est défini à l’aide de majuscules dans Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

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

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte LinkedIn, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez un compte LinkedIn en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ dans votre éditeur. Ce fichier se trouve dans le [Pack de démarrage de stratégie personnalisée][starter-pack] que vous avez téléchargé dans le cadre de l’une des conditions préalables.
1. Recherchez l’élément _ *ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
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

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

Vous disposez maintenant d’une stratégie configurée afin qu’Azure AD B2C sache comment communiquer avec votre compte LinkedIn. Essayez de charger le fichier d’extension de votre stratégie pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour le rendre disponible, vous devez créer un doublon d’un modèle de parcours utilisateur existant, puis le modifier afin qu’il dispose également du fournisseur d’identité LinkedIn.

1. Ouvrez le fichier *TrustFrameworkBase.xml* du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte LinkedIn, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelections**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `LinkedInExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. Dans ce cas, l’action effectuée correspond à la communication entre Azure AD B2C et un compte LinkedIn pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’ID la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé. Par exemple : `LinkedIn-OAUTH`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue par le biais d’une application que vous inscrivez dans votre locataire B2C. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInLinkedIn.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInLinkedIn`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignLinkedIn).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.

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
2. Enregistrez le jeton d’accès LinkedIn dans une revendication. [Les instructions se trouvent ici](idp-pass-through-custom.md).
3. Ajoutez le fournisseur de revendications ci-dessous qui effectue la demande à l’API `/emailAddress` de LinkedIn. Pour autoriser cette demande, vous avez besoin du jeton d’accès LinkedIn.

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

4. Ajoutez l’étape d’orchestration ci-dessous à votre parcours utilisateur, de sorte que le fournisseur de revendications d’API se déclenche quand un utilisateur se connecte avec LinkedIn. Veillez à mettre à jour le numéro `Order` de façon appropriée. Ajoutez cette étape de suite après celle d’orchestration qui déclenche le profil technique LinkedIn.

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
