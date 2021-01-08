---
title: Configurer l’inscription et la connexion avec un compte Twitter
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Twitter dans vos applications avec Azure Active Directory B2C.
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
ms.openlocfilehash: 7779730b98630d08af046e7cb402caca1d0c2fe6
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653654"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Créer une application

Pour utiliser Twitter comme fournisseur d’identité dans Azure AD B2C, vous devez créer une application Twitter. Si vous ne disposez pas encore d’un compte Twitter, vous pouvez en créer un à l’adresse [https://twitter.com/signup](https://twitter.com/signup).

1. Connectez-vous au site web [Développeurs Twitter](https://developer.twitter.com/en/apps) avec les informations d’identification de votre compte Twitter.
1. Sélectionnez **Créer une application**.
1. Entrez un **Nom de l’application** et une **Description de l’application**.
1. Dans **URL du site web**, entrez `https://your-tenant.b2clogin.com`. Remplacez `your-tenant` par le nom de votre locataire. Par exemple : `https://contosob2c.b2clogin.com`.
1. Dans le champ **Callback URL** (URL de rappel), entrez `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Remplacez `your-tenant` par le nom de votre locataire et `your-user-flow-Id` par l’identificateur de votre flux d’utilisateur. Par exemple : `b2c_1A_signup_signin_twitter`. Quand vous entrez le nom de votre locataire et l’ID de flux utilisateur, vous ne devez utiliser que des minuscules, même s’ils sont définis avec des majuscules dans Azure AD B2C.
1. Au bas de la page, lisez et acceptez les termes du contrat, puis sélectionnez **Créer**.
1. Dans la page **Détails de l’application**, sélectionnez **Modifier > Modifier les détails**, cochez la case **Activer la connexion avec Twitter**, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Clés et jetons** et prenez note des valeurs **Clé API de consommateur** et **Clé secrète API de consommateur** à utiliser ultérieurement.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Twitter**.
1. Saisissez un **Nom**. Par exemple, *Twitter*.
1. Pour **ID client**, entrez la clé d’API Consommateur de l’application Twitter que vous avez créée précédemment.
1. Pour **Clé secrète client**, entrez la clé secrète d’API Consommateur que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète que vous avez enregistré dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `TwitterSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Encryption`.
10. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Twitter, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Twitter en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Remplacez la valeur de **client_id** par la clé client que vous avez enregistrée précédemment.
5. Enregistrez le fichier .

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre compte Twitter. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous pouvez créer un doublon d’un modèle de parcours utilisateur et le modifier afin qu’il dispose également du fournisseur d’identité Twitter.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte Twitter, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `TwitterExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. Ici, l’action est la communication d’Azure AD B2C avec un compte Twitter pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’ID la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé. Par exemple : `Twitter-OAUTH1`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Ajouter un fournisseur d’identité Twitter à un workflow d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur que vous souhaitez utiliser pour le fournisseur d’identité Twitter.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Twitter**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInTwitter.xml*.
1. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInTwitter`.
1. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_twitter`
1. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignTwitter).
1. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
1. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.

::: zone-end