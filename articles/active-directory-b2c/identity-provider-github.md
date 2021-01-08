---
title: Configurer l’inscription et la connexion avec un compte GitHub
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes GitHub dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 68ffde11059de4809e519c1ac4f79503f25b0004
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653739"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte GitHub à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>Créer une application GitHub OAuth

Pour utiliser un compte GitHub en tant que [fournisseur d’identité](authorization-code-flow.md) dans Azure Active Directory B2C (Azure AD B2C), vous devez créer dans votre locataire une application qui le représente. Si vous ne disposez pas déjà d’un compte GitHub, vous pouvez vous inscrire sur [https://www.github.com/](https://www.github.com/).

1. Connectez-vous au site web [Développeur GitHub](https://github.com/settings/developers) avec vos informations d’identification GitHub.
1. Sélectionnez **Applications OAuth**, puis **Nouvelle application OAuth**.
1. Entrez le **Nom de l’application** et l’**URL de la page d’accueil**.
1. Entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans l’**URL de rappel d’autorisation**. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Utilisez uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
1. Cliquez sur **Register application**.
1. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configuration d’un compte GitHub en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **GitHub (Préversion)**.
1. Saisissez un **Nom**. Par exemple, *GitHub*.
1. Dans **ID client**, entrez l’ID client de l’application GitHub que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `GitHubSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte GitHub, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte GitHub en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAUTH2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
1. Enregistrez le fichier .

### <a name="add-the-claims-transformations"></a>Ajouter les transformations de revendications

Le profil technique GitHub nécessite l’ajout des transformations de revendication de **CreateIssuerUserId** à la liste des transformations de revendication. Si vous n’avez pas d’élément **ClaimsTransformations** défini dans votre fichier, ajoutez les éléments XML parents, comme indiqué ci-dessous. Les transformations de revendications nécessitent en outre la définition d’un nouveau type de revendication nommé **numericUserId**.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez la revendication numericUserId à l’élément **ClaimsSchema**.
1. Localisez l’élément [ClaimsTransformations](claimstransformations.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez les transformations de revendications CreateIssuerUserId à l’élément **ClaimsTransformations**.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre compte GitHub. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous pouvez créer un doublon d’un modèle de parcours utilisateur et le modifier afin qu’il dispose également du fournisseur d’identité GitHub.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInGitHub`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte GitHub, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `GitHubExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. Dans ce cas, l’action est la communication d’Azure AD B2C avec un compte GitHub pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser la même valeur d’ID que celle utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé. Par exemple : `GitHub-OAuth`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-github-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité GitHub à un flux d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur que vous souhaitez utiliser pour le fournisseur d’identité GitHub.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **GitHub**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInGitHub.xml*.
1. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInGitHub`.
1. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_github`
1. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignGitHub).
1. Enregistrez vos modifications et chargez le fichier.
1. Sous **Stratégies personnalisées**, sélectionnez **B2C_1A_signup_signin**.
1. Pour **Sélectionner une application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant** et sélectionnez GitHub pour vous connecter avec GitHub et tester la stratégie personnalisée.

::: zone-end
