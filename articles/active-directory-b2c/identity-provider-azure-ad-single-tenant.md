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
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 05c4d36f266fb526a1d0232cc32f0408e4322c80
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654385"
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

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

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

1. Sélectionnez **Inscription**. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.
1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.
1. Entrez une **description** pour le secret, sélectionnez une date d’expiration, puis sélectionnez **Ajouter**. Enregistrez la **Valeur** du secret pour l’utiliser à une étape ultérieure.

### <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Si vous souhaitez obtenir les revendications `family_name` et `given_name` à partir d'Azure AD, vous pouvez configurer des revendications facultatives pour votre application dans l'interface utilisateur du portail Azure ou dans le manifeste de l'application. Pour plus d'informations, consultez [Procédure : Fournir des revendications facultatives à votre application Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com). Recherchez et sélectionnez **Azure Active Directory**.
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
1. Pour l'**Étendue**, entrez `openid profile`.
1. Conservez les valeurs par défaut pour **Type de réponse** et **Mode de réponse**.
1. (Facultatif) Pour l'**Indication de domaine**, entrez `contoso.com`. Pour plus d’informations, consultez [Configurer la connexion directe avec Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Sous **Mappage des revendications du fournisseur d’identité**, sélectionnez les revendications suivantes :

    - **ID d’utilisateur** : *oid*
    - **Nom d’affichage** : *name*
    - **Prénom** : *given_name*
    - **Nom** : *family_name*
    - **Adresse e-mail** : *preferred_username*

1. Sélectionnez **Enregistrer**.

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

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’Azure AD, vous devez définir Azure AD en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
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

1. Mettez à jour l’ID de l’élément **TechnicalProfile**. Cet ID est utilisé pour faire référence à ce profil technique à partir d’autres parties de la stratégie, par exemple `OIDC-Contoso`.
1. Mettez à jour la valeur de **DisplayName**. Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion.
1. Mettez à jour la valeur de **Description**.
1. Azure AD utilisant le protocole OpenID Connect, vérifiez que la valeur de **Protocol** est bien `OpenIdConnect`.
1. Définissez la valeur de **METADATA** sur `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, où `tenant-name` est le nom de votre locataire Azure AD. Par exemple : `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
1. Sous **CryptographicKeys**, mettez à jour la valeur de **StorageReferenceId** sur le nom de la clé de stratégie que vous avez créée précédemment. Par exemple : `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre répertoire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
1. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
1. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est encore disponible dans aucune des pages d’inscription ou de connexion. Pour le rendre disponible, créez un doublon d’un modèle de parcours utilisateur existant et modifiez-le afin qu’il dispose également du fournisseur d’identité Azure AD :

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
1. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
1. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
1. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInContoso`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité dans une page d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** à Azure AD, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé dans *TrustFrameworkExtensions.xml*.
1. Sous **ClaimsProviderSelections**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD :

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
1. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’**ID** la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `OIDC-Contoso`.

1. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Azure AD à un flux d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur que vous souhaitez utiliser pour le fournisseur d’identité Azure AD.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Contoso Azure AD**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le en *SignUpSignInContoso.xml*.
1. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInContoso`.
1. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Par exemple : `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Mettez à jour la valeur de l’attribut **ReferenceId** dans **DefaultUserJourney** pour qu’elle corresponde à l’ID du parcours utilisateur que vous avez créé précédemment. Par exemple, *SignUpSignInContoso*.
1. Enregistrez vos modifications et téléchargez le fichier.
1. Sous **Stratégies personnalisées**, sélectionnez la nouvelle stratégie dans la liste.
1. Dans la liste déroulante **Sélectionner une application**, sélectionnez l’application Azure AD B2C que vous avez créée précédemment. Par exemple, *testapp1*.
1. Copiez le point de terminaison **Exécuter maintenant** et ouvrez-le dans une fenêtre de navigation privée, par exemple en mode navigation privée dans Google Chrome ou dans une fenêtre InPrivate dans Microsoft Edge. L’ouverture dans une fenêtre de navigation privée vous permet de tester le parcours utilisateur complet en n’utilisant pas les informations d’identification Azure AD actuellement mises en cache.
1. Sélectionnez le bouton de connexion Azure AD, par exemple *Contoso Employee* (Employé Contoso), puis entrez les informations d’identification d’un utilisateur dans votre locataire d’organisation Azure AD. Vous êtes invité à autoriser l’application, puis à entrer des informations pour votre profil.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous utilisez des stratégies personnalisées, vous pouvez parfois avoir besoin d’informations supplémentaires lors du dépannage d’une stratégie pendant son développement.

Pour faciliter le diagnostic des problèmes, vous pouvez placer temporairement la stratégie en « mode développeur » et collecter des journaux avec Azure Application Insights. Pour savoir comment procéder, consultez [Azure Active Directory B2C : collecte des journaux](troubleshoot-with-application-insights.md).

::: zone-end