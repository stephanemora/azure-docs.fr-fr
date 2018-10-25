---
title: Configurer la connexion avec un compte Google dans Azure Active Directory B2C à l’aide de stratégies personnalisées | Microsoft Docs
description: Configurez la connexion avec un compte Google dans Azure Active Directory B2C à l’aide de stratégies personnalisées.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f4aaef65620a2c6f268f123544c7ecf71dccb82
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887272"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte Google à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’un compte Google à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prérequis

- Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Si vous ne disposez pas d’un compte Google, [créez-en un](https://accounts.google.com/SignUp).

## <a name="register-the-application"></a>Enregistrement de l’application

Pour autoriser la connexion d’utilisateurs à partir d’un compte Google, vous devez créer un projet d’application Google. 

1. Connectez-vous à la [console Google Developers](https://console.developers.google.com/) avec les informations d’identification de votre compte.
2. Entrez un **Nom du projet**, cliquez sur **Créer**, puis vérifiez que vous utilisez le nouveau projet.
3. Sélectionnez **Informations d’identification** dans le menu de gauche, puis **Créer des informations d’identification > ID client OAuth**.
4. Sélectionnez **Configurer l’écran de consentement**.
5. Sélectionnez ou spécifiez une **adresse e-mail** valide, indiquez un **nom de produit** présenté aux utilisateurs, saisissez `b2clogin.com` dans **Domaines autorisés**, puis cliquez sur **Enregistrer**.
6. Sous **Type d’application**, sélectionnez **Application web**.
7. Entrez un **nom** pour votre application.
8. Dans **Origines JavaScript autorisées**, saisissez `https://your-tenant-name.b2clogin.com`. Dans **URI de redirection autorisés**, saisissez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez your-tenant-name par le nom de votre locataire. Vous devez utiliser uniquement des minuscules quand vous entrez le nom de votre locataire, même si le locataire est défini à l’aide de majuscules dans Azure AD B2C.
8. Cliquez sur **Créer**.
9. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google comme fournisseur d’identité dans votre locataire. La clé secrète client constitue des informations d’identification de sécurité importantes.

## <a name="create-a-policy-key"></a>Créer une clé de stratégie

Vous devez stocker la clé secrète client précédemment enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Dans le coin supérieur gauche du portail Azure, choisissez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité - PRÉVERSION**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez le **nom** de la clé de stratégie. Par exemple : `GoogleSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajouter un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Google, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir un compte Google en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
5. Enregistrez le fichier .

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre répertoire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** du locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez l’option **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscrire le fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous créez un doublon d’un modèle de parcours utilisateur et le modifiez afin qu’il dispose également du fournisseur d’identité Azure AD :

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`, puis et copiez l’intégralité de son contenu.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte Google, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez une valeur appropriée pour **TargetClaimsExchangeId**, par exemple `GoogleExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. Dans ce cas, l’action est la communication d’Azure AD B2C avec un compte Google pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour **l’ID** la valeur que pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```
    
    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’**ID** du profil technique que vous avez créé précédemment. Par exemple : `Google-OAuth`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Créer une application Azure AD B2C

La communication avec Azure AD B2C s’effectue via une application que vous créez dans votre locataire. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Dans le coin supérieur gauche du portail Azure, choisissez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application, par exemple, *testapp1*.
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour l’**URL de réponse**.
7. Cliquez sur **Créer**.

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie du fichier *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInGoogle.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInGoogle`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Par exemple : `http://contoso.com/B2C_1A_signup_signin_google`
4. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignGoogle).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.
