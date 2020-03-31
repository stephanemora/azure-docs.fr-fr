---
title: Configurer la connexion avec un compte Azure AD en utilisant des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Configurez la connexion avec un compte Azure Active Directory dans Azure Active Directory B2C à l’aide de stratégies personnalisées.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a6c85ebed7271655745de45694542fb359836e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188408"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte Azure Active Directory à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment activer la connexion d’utilisateurs à partir d’une organisation Azure Active Directory (Azure AD) à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Inscrire une application

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

    Par exemple : `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Sélectionnez **Inscription**. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.
1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.
1. Entrez une **description** pour le secret, sélectionnez une date d’expiration, puis sélectionnez **Ajouter**. Enregistrez la **Valeur** du secret pour l’utiliser à une étape ultérieure.

## <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Si vous souhaitez obtenir les revendications `family_name` et `given_name` à partir d'Azure AD, vous pouvez configurer des revendications facultatives pour votre application dans l'interface utilisateur du portail Azure ou dans le manifeste de l'application. Pour plus d'informations, consultez [Procédure : Fournir des revendications facultatives à votre application Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com). Recherchez et sélectionnez **Azure Active Directory**.
1. Dans la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.
1. Dans la section **Gérer**, sélectionnez **Configuration de jetons (préversion)** .
1. Sélectionnez **Ajouter une revendication facultative**.
1. Sélectionnez le type de jeton que vous souhaitez configurer.
1. Sélectionnez les revendications facultatives à ajouter.
1. Cliquez sur **Add**.

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

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD :

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
1. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’**ID** la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `OIDC-Contoso`.

1. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue par le biais d’une application que vous inscrivez dans votre locataire B2C. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

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
