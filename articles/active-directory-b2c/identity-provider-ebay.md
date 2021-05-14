---
title: Configurer l’inscription et la connexion avec un compte eBay
titleSuffix: Azure AD B2C
description: Offrez aux clients disposant de comptes eBay la possibilité de s’inscrire et de se connecter à vos applications en utilisant Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e2be6fd7ee6d42fe22e4735420ff2b16fa9974ba
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285319"
---
# <a name="set-up-sign-up-and-sign-in-with-an-ebay-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte eBay en utilisant Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-ebay-application"></a>Créer une application eBay

Pour permettre aux utilisateurs avec un compte eBay de se connecter dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans la [console des développeurs eBay](https://developer.ebay.com). Pour plus d’informations, consultez [Création d’un compte de développeur](https://developer.ebay.com/api-docs/static/creating-edp-account.html). Si vous ne disposez pas encore d’un compte de développeur eBay, créez-en un sur [https://developer.ebay.com/signin](https://developer.ebay.com/signin?tab=register).

Pour créer une application eBay, suivez ces étapes :

1. Connectez-vous à [Application Keys](https://developer.ebay.com/my/keys) (Clés d’application) de la console de développement eBay avec les informations d’identification de votre compte de développeur eBay.
1. Entrez un titre d’application dans **Application Title**.
1. Sous **Production**, sélectionnez **Create a keyset** (Créer un jeu de clés). 
1. Dans la page **Confirm the Primary Contact for this Account** (Confirmer le contact principal pour ce compte), fournissez les détails de votre compte. Pour effectuer le processus d’inscription, sélectionnez **Continue to Create Keys** (Continuer pour créer des clés).
1. Copiez les valeurs de **App ID (Client ID)** et de **App ID (Client ID)** . Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.
1. Sélectionnez **User Tokens** (Jetons d’utilisateur), puis **Get a Token from eBay via Your Application** (Obtenir un jeton auprès d’eBay via votre application).
1. Sélectionnez **Add eBay Redirect URL** (Ajouter une URL de redirection eBay).
    1. Entrez une URL valide de votre politique de confidentialité dans le champ **Your privacy policy URL**, par exemple `https://www.contoso.com/privacy`. L’URL de stratégie est une page que vous tenez à jour pour fournir des informations de confidentialité pour votre application.
    1. Dans le champ **Your auth accepted URL**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé.
1. Sélectionnez **Enregistrer**.

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `eBaySecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="configure-ebay-as-an-identity-provider"></a>Configuration d’eBay en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec un compte eBay, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte eBay en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <Domain>ebay.com</Domain>
        <DisplayName>eBay</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="eBay-OAUTH2">
            <DisplayName>eBay</DisplayName>
            <Protocol Name="OAuth2" />
            <Metadata>
              <Item Key="ProviderName">ebay.com</Item>
              <Item Key="authorization_endpoint">https://auth.ebay.com/oauth2/authorize</Item>
              <Item Key="AccessTokenEndpoint">https://api.ebay.com/identity/v1/oauth2/token</Item>
              <Item Key="ClaimsEndpoint">https://apiz.ebay.com/commerce/identity/v1/user/</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
              <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
              <Item Key="scope">https://api.ebay.com/oauth/api_scope/commerce.identity.readonly</Item>
              <Item Key="UsePolicyInRedirectUri">0</Item>
              <!-- Update the Client ID below to the Application ID -->
              <Item Key="client_id">Your eBay app ID</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="client_secret" StorageReferenceId="eBaySecret"/>
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="individualAccount.firstName"/>
              <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="individualAccount.lastName"/>
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
              <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="ebay.com" AlwaysUseDefaultValue="true" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            </OutputClaims>
            <OutputClaimsTransformations>
              <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
              <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
              <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

4. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
5. Enregistrez le fichier .


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="eBayExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="eBayExchange" TechnicalProfileReferenceId="eBay-OAUTH2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **eBay** pour vous connecter avec un compte eBay.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transmettre un jeton Facebook à votre application](idp-pass-through-user-flow.md).

::: zone-end
