---
title: Configurer la connexion avec un compte Azure Active Directory dans Azure Active Directory B2C à l’aide de stratégies personnalisées | Microsoft Docs
description: Configurez la connexion avec un compte Azure Active Directory dans Azure Active Directory B2C à l’aide de stratégies personnalisées.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8ade695f1930c11f2d2d7ab7e0430910bd6f8705
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508642"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte Azure Active Directory à l’aide de stratégies personnalisées dans Azure Active Directory B2C 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure Active Directory (Azure AD) à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory(Azure AD) B2C.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Inscrire une application

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser le répertoire contenant le locataire Azure AD de l’organisation (contoso.com) en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant le répertoire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription d’application**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Pour le **Type d’application**, sélectionnez `Web app / API`.
7. Pour le champ **URL de connexion**, entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C :

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Par exemple : `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

8. Cliquez sur **Créer**. Copiez **l’ID d’application** pour une utilisation ultérieure.
9. Sélectionnez l’application, puis **Paramètres**.
10. Sélectionnez **Clés**, entrez la description de la clé, sélectionnez une durée, puis cliquez sur **Enregistrer**. Copiez la valeur de la clé qui est affichée pour une utilisation ultérieure.

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé d’application que vous avez créée dans votre locataire Azure AD B2C.

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
4. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
5. Pour **Options**, choisissez `Manual`.
6. Entrez un **nom** pour la clé de stratégie. Par exemple : `ContosoAppSecret`.  Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
7. Dans **Secret**, entrez la clé d’application que vous avez enregistrée.
8. Pour **Utilisation de la clé**, sélectionnez `Signature`.
9. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’Azure AD, vous devez définir Azure AD en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" AlwaysUseDefaultValue="true" />
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

1. Mettez à jour l’ID de l’élément **TechnicalProfile**. Cet ID est utilisé pour faire référence à ce profil technique à partir d’autres parties de la stratégie.
2. Mettez à jour la valeur de **DisplayName**. Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion.
3. Mettez à jour la valeur de **Description**.
4. Azure AD utilisant le protocole OpenID Connect, vérifiez que la valeur de **Protocol** est bien `OpenIdConnect`.
5. Définissez la valeur de **METADATA** sur `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, où `your-AD-tenant-name` est le nom de votre locataire Azure AD. Par exemple, `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
6. Ouvrez votre navigateur, puis accédez à l’URL **METADATA** que vous venez de mettre à jour, recherchez l’objet **issuer**, puis copiez et collez la valeur dans **ProviderName** dans le fichier XML.
8. Définissez **client_id** et **IdTokenAudience** sur l’ID d’application de l’inscription de l’application.
9. Sous **CryptograhicKeys**, mettez à jour la valeur de **StorageReferenceId** sur la clé de stratégie que vous avez définie. Par exemple : `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre répertoire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour le rendre disponible, vous devez créer un doublon d’un modèle de parcours utilisateur existant et le modifier afin qu’il dispose également du fournisseur d’identité Azure AD :

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInContoso`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** à Azure AD, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelections**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `ContosoExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD :

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’**ID** la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `ContosoProfile`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue via une application que vous créez dans votre locataire. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application (par exemple, *testapp1*).
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour l’**URL de réponse**.
7. Cliquez sur **Créer**.

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le en *SignUpSignInContoso.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInContoso`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_contoso`
4. Mettez à jour la valeur de l’attribut **ReferenceId** dans **DefaultUserJourney** pour qu’elle corresponde à l’ID du nouveau parcours utilisateur que vous avez créé (SignUpSignInContoso).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.

