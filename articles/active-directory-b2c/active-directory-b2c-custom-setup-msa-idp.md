---
title: Configurer la connexion avec un compte Microsoft en utilisant des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Guide pratique pour utiliser des stratégies personnalisées visant à activer un compte Microsoft (MSA) comme fournisseur d’identité avec le protocole OIDC (OpenID Connect).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 393e6f0b87cbd6a548825276da3f59863e2833eb
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948366"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un compte Microsoft à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment activer la connexion d’utilisateurs à partir d’un compte Microsoft à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prérequis

- Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Si vous n’avez pas de compte Microsoft, créez-en un sur [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Ajouter une application

Pour autoriser la connexion des utilisateurs avec un compte Microsoft, vous devez inscrire une application dans un locataire Azure AD. Le locataire Azure AD n’est pas identique à votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple, *MSAapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** .
1. Sous **URI de redirection (facultatif)** , sélectionnez **Web**, puis entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` dans la zone de texte. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C.
1. Sélectionnez **Inscrire**.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page Vue d’ensemble de l’application. Vous en aurez besoin au moment de la configuration du fournisseur de revendications dans l’une des sections suivantes.
1. Sélectionnez **Certificats et secrets**.
1. Cliquez sur **Nouveau secret client**.
1. Entrez une **Description** pour le secret, par exemple *Clé secrète client d’application MSA*, puis cliquez sur **Ajouter**.
1. Enregistrez le mot de passe de l’application affiché dans la colonne **Valeur**. Vous utiliserez cette valeur dans la section suivante.

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Maintenant que vous avez créé l’application dans votre locataire Azure AD, vous devez stocker la clé secrète client de cette application dans votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `MSASecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez notée dans la section précédente.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Microsoft, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure Active Directory BC2 peut communiquer grâce à un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant l’élément **ClaimsProvider** au fichier d’extension de votre stratégie.

1. Ouvrez le fichier de stratégie *TrustframeworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Remplacez la valeur de **client_id** par *l’ID d’application (client)* de l’application Azure AD que vous avez enregistré précédemment.
1. Enregistrez le fichier .

Vous avez maintenant configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre application de compte Microsoft dans Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

Avant de continuer, téléchargez la stratégie modifiée pour confirmer qu’il n’y a aucun problème à ce jour.

1. Accédez à votre locataire Azure AD B2C dans le portail Azure et sélectionnez **Identity Experience Framework**.
1. Dans la page **Stratégies personnalisées**, sélectionnez **Télécharger la stratégie personnalisée**.
1. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
1. Cliquez sur **Télécharger**.

Si aucune erreur n’est affichée dans le portail, passez à la section suivante.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, vous avez défini le fournisseur d’identité, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, créez un doublon d’un modèle de parcours utilisateur et modifiez-le afin qu’il dispose également du fournisseur d’identité du compte Microsoft.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
1. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
1. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
1. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInMSA`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte Microsoft, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Dans le fichier *TrustFrameworkExtensions.xml*, recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
1. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `MicrosoftAccountExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action consiste, dans ce cas, à permettre à Azure AD B2C de communiquer avec un compte Microsoft pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
1. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’ID la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Mettez à jour la valeur**TechnicalProfileReferenceId** pour qu’elle corresponde à la valeur `Id` de l’élément **TechnicalProfile** du fournisseur de revendications que vous avez ajouté précédemment. Par exemple : `MSA-OIDC`.

1. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue par le biais d’une application que vous inscrivez dans votre locataire B2C. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInMSA.xml*.
1. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInMSA`.
1. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_msa`
1. Mettez à jour la valeur de l’attribut **ReferenceId** dans **DefaultUserJourney** pour qu’elle corresponde à l’ID du parcours utilisateur que vous avez créé précédemment (SignUpSignInMSA).
1. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
1. Assurez-vous que l’application Azure AD B2C que vous avez créée dans la section précédente (ou en remplissant les conditions préalables, par exemple *webapp1* ou*testapp1*) est sélectionnée dans le champ **Sélectionner l’application**, puis testez-la en cliquant sur **Exécuter maintenant**.
1. Sélectionnez le bouton **compte Microsoft** et connectez-vous.

    Si l’opération de connexion réussit, vous serez redirigé vers `jwt.ms` qui affiche le jeton décodé qui ressemble à ceci :

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
