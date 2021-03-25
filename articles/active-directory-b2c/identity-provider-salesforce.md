---
title: Configurer l’inscription et la connexion avec un compte Salesforce
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Salesforce dans vos applications avec Azure Active Directory B2C.
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
ms.openlocfilehash: d445a4e1d0fd39788c18f80b25ce02a7c5d0a243
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488684"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Salesforce à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Créer une application Salesforce

Pour permettre aux utilisateurs de se connecter avec un compte Salesforce dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans votre [App Manager](https://login.salesforce.com/) Salesforce. Pour plus d’informations, consultez [Configurer les paramètres de l’application connectée de base](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm) et [Activer les paramètres OAuth pour l’intégration d’API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Connectez-vous à Salesforce](https://login.salesforce.com/).
1. Dans le menu, sélectionnez **Configurer**.
1.  Développez **Applications**, puis sélectionnez **App Manager**.
1. Sélectionnez **Nouvelle application connectée**.
1. Sous **Informations de base**, entrez les informations suivantes :
    1. **Nom de l’application connectée**  : le nom de l’application connectée s’affiche dans App Manager et dans la vignette du lanceur d’applications. Ce nom doit être unique dans l’organisation. 
    1. **Nom de l’API** 
    1. **Adresse de contact**  : adresse e-mail de contact Salesforce
1. Sous **API (activer les paramètres OAuth)** , sélectionnez **Activer les paramètres OAuth**.
    1. Dans le champ **Callback URL** (URL de rappel), entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
    1. Dans **Étendues OAuth sélectionnées**, sélectionnez **Accéder à vos informations de base (ID, profil, adresse de messagerie, adresse, téléphone)** , puis **Autoriser l’accès à votre identificateur unique (OpenID)** .
    1. Sélectionnez **Exiger un secret pour le flux de serveur web**.
1. Sélectionnez **Configurer le jeton d’ID**. 
    1. Définissez le paramètre **Jeton valide pendant** sur 5 minutes.
    1. Sélectionnez **Inclure les revendications standard**.
1. Cliquez sur **Enregistrer**.
1. Copiez les valeurs de **Clé du client** et de **Secret du client**. Vous aurez besoin de ces deux valeurs pour configurer Salesforce comme fournisseur d’identité dans votre locataire. **Client secret** est une information d’identification de sécurité importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurer Salesforce en tant que fournisseur d’identité

1. Veillez à bien utiliser le répertoire qui contient le locataire Azure AD B2C. Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut, puis choisissez l’annuaire qui contient votre locataire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Salesforce*.
1. Pour **URL de métadonnées**, entrez l’URL du [document de configuration de Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Pour un bac à sable, login.salesforce.com est remplacé par test.salesforce.com. Pour une communauté, login.salesforce.com est remplacé par l’URL de la communauté, par exemple username.force.com/.well-known/openid-configuration. L’URL doit être HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Dans **ID client**, entrez l’ID d’application que vous avez enregistré précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée précédemment.
1. Pour l'**Étendue**, entrez `openid id profile email`.
1. Conservez les valeurs par défaut pour **Type de réponse** et **Mode de réponse**.
1. (Facultatif) Pour l'**Indication de domaine**, entrez `contoso.com`. Pour plus d’informations, consultez [Configurer la connexion directe avec Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Sous **Mappage des revendications du fournisseur d’identité**, sélectionnez les revendications suivantes :

    - **ID utilisateur** : *sub*
    - **Nom d’affichage** : *name*
    - **Prénom** : *given_name*
    - **Nom** : *family_name*
    - **Adresse e-mail** : *email*

1. Sélectionnez **Enregistrer**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Salesforce à un flux utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux utilisateur auquel vous souhaitez ajouter le fournisseur d’identité Salesforce.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Salesforce**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux utilisateur**.
1. Sur la page d'inscription ou de connexion, sélectionnez **Salesforce** pour vous connecter avec un compte Salesforce.

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
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `SalesforceSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurer Salesforce en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter à l’aide d’un compte Salesforce, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Salesforce en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. L’élément **MÉTADONNÉES** est défini sur l’URL du [document de configuration de Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Pour un bac à sable, login.salesforce.com est remplacé par test.salesforce.com. Pour une communauté, login.salesforce.com est remplacé par l’URL de la communauté, par exemple username.force.com/.well-known/openid-configuration. L’URL doit être HTTPS.
5. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
6. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Sur la page d'inscription ou de connexion, sélectionnez **Salesforce** pour vous connecter avec un compte Salesforce.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transmettre un jeton Salesforce à votre application](idp-pass-through-user-flow.md).
