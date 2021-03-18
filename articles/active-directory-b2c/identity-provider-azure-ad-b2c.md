---
title: Configurer l’inscription et la connexion avec un compte Azure AD B2C d’un autre locataire Azure AD B2C
titleSuffix: Azure AD B2C
description: Fournissez l’inscription et la connexion aux clients disposant d’un compte Azure AD B2C d’un autre locataire dans vos applications utilisant Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c9a20305f05b285b29d4a5eaf75116c862f3a6d4
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448487"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configurer l’inscription et la connexion avec un compte Azure AD B2C d’un autre locataire Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment configurer une fédération avec un autre locataire Azure AD B2C. Quand vos applications sont protégées avec votre Azure AD B2C, cela permet aux utilisateurs d’autres Azure AD B2C de se connecter avec leurs comptes existants. Dans le diagramme suivant, les utilisateurs sont en mesure de se connecter à une application protégée par Azure AD B2C de *Contoso* avec un compte managé par un locataire Azure AD B2C de *Fabrikam*. 

![Fédération Azure AD B2C avec un autre locataire Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

Pour activer la connexion des utilisateurs avec un compte d’un autre locataire Azure AD B2C (par exemple Fabrikam) dans votre Azure AD B2C (par exemple Contoso) :

1. Créez un [flux d’utilisateur](tutorial-create-user-flows.md) ou une [stratégie personnalisée](custom-policy-get-started.md).
1. Créez ensuite une application dans Azure AD B2C, comme décrit dans cette section. 

Pour créer une application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre autre locataire Azure AD B2C (par exemple Fabrikam.com).
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple *ContosoApp*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** .
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez l’URL suivante en minuscules, en remplaçant `your-B2C-tenant-name` par le nom de votre locataire Azure AD B2C (par exemple Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Par exemple : `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Sous Autorisations, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline_access**.
1. Sélectionnez **Inscription**.
1. Dans la page **Azure AD B2C – Inscriptions d’applications**, sélectionnez l’application que vous avez créée, par exemple *ContosoApp*.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page Vue d’ensemble de l’application. Vous en aurez besoin au moment de configurer le fournisseur d’identité dans la section suivante.
1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Certificats et secrets**.
1. Sélectionnez **Nouveau secret client**.
1. Entrez une description pour la clé secrète client dans la zone **Description**. Par exemple, *clientsecret1*.
1. Sous **Expire**, sélectionnez une durée pendant laquelle le secret est valide, puis sélectionnez **Ajouter**.
1. Enregistrez la **Valeur** du secret. Vous en aurez besoin au moment de configurer le fournisseur d’identité dans la section suivante.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurer Azure AD B2C en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Assurez-vous que vous utilisez le répertoire qui contient le locataire Azure AD B2C pour lequel vous souhaitez configurer la fédération (par exemple Contoso). Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut et choisissez l’annuaire qui contient votre locataire Azure AD B2C (par exemple Contoso).
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Fabrikam*.
1. Dans **URL des métadonnées**, entrez l’URL suivante en remplaçant `{tenant}` par le nom de domaine de votre locataire Azure AD B2C (par exemple Fabrikam). Remplacez `{policy}` par le nom de la stratégie que vous configurez dans l’autre locataire :

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Par exemple : `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Dans **ID client**, entrez l’ID d’application que vous avez enregistré précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée précédemment.
1. Pour l'**Étendue**, entrez `openid`.
1. Conservez les valeurs par défaut pour **Type de réponse** et **Mode de réponse**.
1. Facultatif : pour l’**Indication du domaine**, entrez le nom de domaine que vous souhaitez utiliser pour la [connexion directe](direct-signin.md#redirect-sign-in-to-a-social-provider). Par exemple *fabrikam.com*.
1. Sous **Mappage des revendications du fournisseur d’identité**, sélectionnez les revendications suivantes :

    - **ID utilisateur** : *sub*
    - **Nom d’affichage** : *name*
    - **Prénom** : *given_name*
    - **Nom** : *family_name*
    - **Adresse e-mail** : *email*

1. Sélectionnez **Enregistrer**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Azure AD B2C à un flux d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité Azure AD B2C.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Fabrikam**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Fabrikam** pour vous connecter avec l’autre locataire Azure AD B2C.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé d’application que vous avez créée auparavant dans votre locataire Azure AD B2C.

1. Assurez-vous que vous utilisez le répertoire qui contient le locataire Azure AD B2C pour lequel vous souhaitez configurer la fédération (par exemple Contoso). Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut et choisissez l’annuaire qui contient votre locataire Azure AD B2C (par exemple Contoso).
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `FabrikamAppSecret`.  Le préfixe `B2C_1A_` étant ajouté automatiquement au nom de votre clé lors de sa création, sa référence dans le code XML de la section suivante est à *B2C_1A_FabrikamAppSecret*.
1. Dans **Secret**, entrez le secret client que vous avez enregistré précédemment.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurer Azure AD B2C en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter à l’aide d’un compte d’un autre locataire Azure AD B2C, vous devez définir l’autre Azure AD B2C en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD B2C comme fournisseur de revendications en ajoutant Azure AD B2C à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Mettez à jour les éléments XML suivants avec la valeur appropriée :

    |Élément XML  |Value  |
    |---------|---------|
    |ClaimsProvider\Domain  | Nom de domaine utilisé pour la [connexion directe](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Entrez le nom de domaine que vous souhaitez utiliser pour la connexion directe. Par exemple *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion. Par exemple *Fabrikam*. |
    |Metadata\client_id|Identificateur d’application du fournisseur d’identité. Mettez à jour l’ID client avec l’ID de l’application que vous avez créée précédemment dans l’autre locataire Azure AD B2C.|
    |Metadata\METADATA|URL qui pointe vers un document de configuration de fournisseur d’identité OpenID Connect, également appelé point de terminaison de configuration OpenID connu. Entrez l’URL suivante en remplaçant `{tenant}` par le nom de domaine de l’autre locataire Azure AD B2C (Fabrikam). Remplacez `{tenant}` par le nom de la stratégie que vous configurez dans l’autre locataire et remplacez `{policy]` par le nom de la stratégie : `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration`. Par exemple : `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Mettez à jour la valeur de **StorageReferenceId** avec le nom de la clé de stratégie que vous avez créée précédemment. Par exemple : `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Fabrikam** pour vous connecter avec l’autre locataire Azure AD B2C.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transmettre l’autre jeton Azure AD B2C à votre application](idp-pass-through-user-flow.md).
