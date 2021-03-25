---
title: Configurer l’inscription et la connexion avec un compte Weibo
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes Weibo dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 272abef5adfbcceebe82ab703152e2a8fde86d9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488552"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Weibo à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-weibo-application"></a>Créer une application Weibo

Pour autoriser la connexion des utilisateurs avec un compte Weibo dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans le portail des développeurs Weibo. Si vous n’avez pas encore de compte Weibo, vous pouvez vous inscrire à l’adresse [https://weibo.com](https://weibo.com/signup/signup.php?lang=en-us).

1. Connectez-vous au portail des développeurs Weibo avec les informations d’identification de votre compte Weibo.
1. Une fois que vous êtes connecté, sélectionnez votre nom d’affichage dans le coin supérieur droit.
1. Dans la liste déroulante, sélectionnez **编辑开发者信息** (Modifier les informations développeur).
1. Entrez les informations requises et sélectionnez **提交** (Envoyer).
1. Finalisez le processus de vérification d’e-mail.
1. Accédez à la page de vérification d’identité.
1. Entrez les informations requises et sélectionnez **提交** (Envoyer).

### <a name="register-a-weibo-application"></a>Inscrire une application Weibo

1. Accédez à la Page d’inscription de l’application Weibo.
1. Entrez les informations nécessaires relatives à l’application.
1. Sélectionnez **创建** (Créer).
1. Copiez les valeurs **Clé d’application** et **Secret d’application**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.
1. Téléchargez les photos requises et entrez les informations nécessaires.
1. Sélectionnez **保存以上信息** (Enregistrer).
1. Sélectionnez **高级信息** (Informations avancées).
1. Sélectionnez **编辑** (Modifier) en regard du champ pour OAuth2.0 **授权设置**(URL de redirection).
1. Pour OAuth 2.0 **授权设置** (URL de redirection), entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé.
1. Sélectionnez **提交** (Envoyer).

::: zone pivot="b2c-user-flow"

## <a name="configure-weibo-as-an-identity-provider"></a>Configurer Weibo en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Weibo (Préversion)**.
1. Saisissez un **Nom**. Par exemple, *Weibo*.
1. Dans **ID client**, entrez la clé d’application de l’application Weibo que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète d’application que vous avez consignée.
1. Sélectionnez **Enregistrer**.

## <a name="add-weibo-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Weibo à un flux d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur que vous souhaitez ajouter au fournisseur d’identité Weibo.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Weibo**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Sur la page d’inscription ou de connexion, sélectionnez **Weibo** pour vous connecter avec un compte Weibo.

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
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `WeiboSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="configure-weibo-as-an-identity-provider"></a>Configurer Weibo en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec un compte Weibo, vous devez définir le compte comme fournisseur de revendications avec lequel Azure AD B2C peut communiquer à travers un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Weibo en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>weibo.com</Domain>
      <DisplayName>Weibo (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAuth2">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">weibo</Item>
            <Item Key="authorization_endpoint">https://api.weibo.com/oauth2/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.weibo.com/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weibo.com/2/account/get_uid.json</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">uid</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="uid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="weibo.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="Weibo User" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="UserId" />
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
    <ClaimsProvider>
    ```

4. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
5. Enregistrez le fichier .

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="WeiboExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeiboExchange" TechnicalProfileReferenceId="Weibo-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Sur la page d’inscription ou de connexion, sélectionnez **Weibo** pour vous connecter avec un compte Weibo.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end
