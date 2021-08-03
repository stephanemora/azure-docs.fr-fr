---
title: Configurer la connexion pour une organisation Azure AD
titleSuffix: Azure AD B2C
description: Configurez la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/26/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a6952679ad2497a059b6ad043ef5e1e23fea0236
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744068"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurer la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure AD spécifique en utilisant un flux utilisateur dans Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Inscrire une application Azure AD

Pour autoriser la connexion des utilisateurs avec un compte Azure AD d’une organisation Azure AD spécifique, dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application via le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Inscrire une application auprès de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD de l’organisation (par exemple, contoso.com). Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple : `Azure AD B2C App`.
1. Acceptez la sélection par défaut de **Comptes dans cet annuaire organisationnel uniquement** pour cette application.
1. Pour le champ **URI de redirection**, acceptez la valeur **Web**, puis entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C :

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Par exemple : `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-domain-name` par le nom de votre domaine personnalisé et `your-tenant-name` par le nom de votre locataire.

1. Sélectionnez **Inscription**. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.
1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.
1. Entrez une **description** pour le secret, sélectionnez une date d’expiration, puis sélectionnez **Ajouter**. Enregistrez la **Valeur** du secret pour l’utiliser à une étape ultérieure.

### <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Si vous souhaitez obtenir les revendications `family_name` et `given_name` à partir d'Azure AD, vous pouvez configurer des revendications facultatives pour votre application dans l'interface utilisateur du portail Azure ou dans le manifeste de l'application. Pour plus d'informations, consultez [Procédure : Fournir des revendications facultatives à votre application Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant le locataire Azure AD de votre organisation. Recherchez et sélectionnez **Azure Active Directory**.
1. Dans la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.
1. Dans la section **Gérer**, sélectionnez **Configuration de jetons**.
1. Sélectionnez **Ajouter une revendication facultative**.
1. Dans **Type de jeton**, sélectionnez **ID**.
1. Sélectionnez les revendications facultatives à ajouter, `family_name` et `given_name`.
1. Cliquez sur **Add**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

1. Veillez à bien utiliser le répertoire qui contient le locataire Azure AD B2C. Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut, puis choisissez l’annuaire qui contient votre locataire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Contoso Azure AD*.
1. Dans **URL des métadonnées**, entrez l’URL suivante en remplaçant `{tenant}` par le nom de domaine de votre locataire Azure AD :

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Par exemple : `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Par exemple : `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Dans **ID client**, entrez l’ID d’application que vous avez enregistré précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée précédemment.
1. Pour **Étendue**, entrez `openid profile`.
1. Conservez les valeurs par défaut pour **Type de réponse** et **Mode de réponse**.
1. (Facultatif) Pour l'**Indication de domaine**, entrez `contoso.com`. Pour plus d’informations, consultez [Configurer la connexion directe avec Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Sous **Mappage des revendications du fournisseur d’identité**, sélectionnez les revendications suivantes :

    - **ID d’utilisateur** : *oid*
    - **Nom d’affichage** : *name*
    - **Prénom** : *given_name*
    - **Nom** : *family_name*
    - **Adresse e-mail** : *preferred_username*

1. Sélectionnez **Enregistrer**.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Azure AD à un flux d’utilisateur 

À ce stade, le fournisseur d’identité Azure AD a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Ajouter le fournisseur d’identité Azure AD à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité Azure AD.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Contoso Azure AD**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`. 
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Contoso Azure AD** pour vous connecter avec un compte Contoso Azure AD.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé d’application que vous avez créée dans votre locataire Azure AD B2C.

1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `ContosoAppSecret`.  Le préfixe `B2C_1A_` étant ajouté automatiquement au nom de votre clé lors de sa création, sa référence dans le code XML de la section suivante est à *B2C_1A_ContosoAppSecret*.
1. Dans **Secret**, entrez le secret client que vous avez enregistré précédemment.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter à l’aide d’un compte Azure AD, vous devez définir Azure AD en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Sous l’élément **ClaimsProvider**, mettez à jour la valeur de **Domaine** sur une valeur unique qui peut être utilisée pour le distinguer des autres fournisseurs d’identité. Par exemple, `Contoso`. Vous ne placez pas de `.com` à la fin de ce paramètre de domaine.
5. Sous l’élément **ClaimsProvider**, mettez à jour la valeur de **DisplayName** sur un nom convivial pour le fournisseur de revendications. Cette valeur n’est pas utilisée actuellement.

### <a name="update-the-technical-profile"></a>Mise à jour du profil technique

Pour obtenir un jeton à partir du point de terminaison Azure AD, vous devez définir les protocoles qu’Azure AD B2C doit utiliser pour communiquer avec Azure AD. Cette opération est effectuée dans l’élément **TechnicalProfile** de **ClaimsProvider**.

1. Mettez à jour l’ID de l’élément **TechnicalProfile**. Cet ID est utilisé pour faire référence à ce profil technique à partir d’autres parties de la stratégie, par exemple `AADContoso-OpenIdConnect`.
1. Mettez à jour la valeur de **DisplayName**. Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion.
1. Mettez à jour la valeur de **Description**.
1. Azure AD utilisant le protocole OpenID Connect, vérifiez que la valeur de **Protocol** est bien `OpenIdConnect`.
1. Définissez la valeur de **METADATA** sur `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, où `tenant-name` est le nom de votre locataire Azure AD. Par exemple : `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
1. Sous **CryptographicKeys**, mettez à jour la valeur de **StorageReferenceId** sur le nom de la clé de stratégie que vous avez créée précédemment. Par exemple : `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Employé Contoso** pour vous connecter avec un compte Contoso Azure AD.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous utilisez des stratégies personnalisées, vous pouvez parfois avoir besoin d’informations supplémentaires lors du dépannage d’une stratégie pendant son développement.

Pour faciliter le diagnostic des problèmes, vous pouvez placer temporairement la stratégie en « mode développeur » et collecter des journaux avec Azure Application Insights. Pour savoir comment procéder, consultez [Azure Active Directory B2C : collecte des journaux](troubleshoot-with-application-insights.md).

::: zone-end
