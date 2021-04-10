---
title: Configurer l’inscription et la connexion avec un compte Amazon
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Amazon dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e152d9c242a44fe869eb7bfe7a16fbd7dfc8049d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580112"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Créer une application dans la console de développement Amazon

Pour permettre la connexion des utilisateurs avec un compte Amazon dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans [Services et technologies Amazon Developer](https://developer.amazon.com). Pour plus d’informations, consultez [S’inscrire pour se connecter avec Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html). Si vous ne disposez pas encore d’un compte Amazon, vous pouvez en créer un à l’adresse [https://www.amazon.com/](https://www.amazon.com/).

1. Connectez-vous à la [console de développement Amazon](https://developer.amazon.com/dashboard) avec les informations d’identification de votre compte Amazon.
1. Si ce n’est déjà fait, cliquez sur **S’inscrire**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
1. Dans le tableau de bord, sélectionnez **connexion avec Amazon**.
1. Sélectionnez **Create a New Security Profile** (Créer un profil de sécurité).
1. Entrez un **nom de profil de sécurité**, une **description du profil de sécurité** et l’**URL de l’avis de confidentialité et de consentement**, par exemple `https://www.contoso.com/privacy` L’URL de l’avis de confidentialité est une page gérée qui fournit aux utilisateurs des informations sur la confidentialité. Ensuite, cliquez sur **Enregistrer**.
1. Dans la section **Connexion avec les configurations Amazon**, sélectionnez le **nom du profil de sécurité** que vous avez créé, cliquez sur l’icône **Gérer** et sélectionnez **Paramètres web**.
1. Dans la section **Paramètres web**, copiez les valeurs **ID client**. Sélectionnez **Afficher le secret** pour obtenir la clé secrète client et copiez-la. Vous en aurez besoin pour configurer un compte Amazon en tant que fournisseur d’identité dans votre locataire. **Client Secret** est une information d’identification de sécurité importante.
1. Dans les **paramètres web**, sélectionnez **Modifier**. 
    1. Dans **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. Remplacez `your-tenant-name` par le nom de votre locataire. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name`.
    1.  Dans **URL de retour autorisées**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`.  Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`.  Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé.
1. Sélectionnez **Enregistrer**.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Configuration d’Amazon en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Amazon**.
1. Saisissez un **Nom**. Par exemple, *Amazon*.
1. Dans **ID client**, entrez l’ID client de l’application Amazon que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Amazon à un flux d’utilisateur 

À ce stade, le fournisseur d’identité Amazon a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Pour ajouter le fournisseur d’identité Amazon à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité Amazon.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Amazon**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Amazon** pour vous connecter avec un compte Amazon.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `AmazonSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="configure-amazon-as-an-identity-provider"></a>Configuration d’Amazon en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec un compte Amazon, vous devez définir le compte en tant que fournisseur de revendications. Azure AD B2C doit pouvoir communiquer avec celui-ci via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Amazon en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.


1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
5. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Amazon** pour vous connecter avec un compte Amazon.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end
