---
title: Configurer la connexion pour les annuaires Azure AD multilocataires par des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Ajoutez un fournisseur d’identité Azure AD multilocataire en utilisant des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ad51e113a752e0692cb377a83d4819b4e284bb7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188432"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer une connexion pour un service Azure Active Directory mutualisé à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à l’aide du point de terminaison mutualisé pour Azure Active Directory (Azure AD) à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure AD B2C. Cela permet aux utilisateurs issus de différents locataires Azure AD de se connecter avec Azure AD B2C sans avoir à configurer un fournisseur d’identité pour chaque locataire. Toutefois, les membres invités dans ces locataires **ne pourront pas** se connecter. Pour ce faire, vous devez [configurer individuellement chaque locataire](identity-provider-azure-ad-single-tenant-custom.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Inscrire une application

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD de l’organisation (par exemple, contoso.com). Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple : `Azure AD B2C App`.
1. Sélectionnez **Comptes dans un annuaire organisationnel** pour cette application.
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
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `AADAppSecret`.  Le préfixe `B2C_1A_` étant ajouté automatiquement au nom de votre clé lors de sa création, sa référence dans le code XML de la section suivante est à *B2C_1A_AADAppSecret*.
1. Dans **Secret**, entrez le secret client que vous avez enregistré précédemment.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’Azure AD, vous devez définir Azure AD en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. Sous l’élément **ClaimsProvider**, mettez à jour la valeur de **Domaine** sur une valeur unique qui peut être utilisée pour le distinguer des autres fournisseurs d’identité.
1. Sous l’élément **TechnicalProfile**, mettez à jour la valeur de **DisplayName**, par exemple `Contoso Employee`. Cette valeur apparaît sur le bouton de connexion dans votre page de connexion.
1. Définissez **client_id** sur l’ID de l’application multilocataire Azure AD que vous avez inscrite précédemment.
1. Sous **CryptographicKeys**, mettez à jour la valeur de **StorageReferenceId** sur le nom de la clé de stratégie que vous avez créée précédemment. Par exemple : `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restriction de l’accès

> [!NOTE]
> La valeur `https://login.microsoftonline.com/` pour **ValidTokenIssuerPrefixes** autorise tous les utilisateurs Azure AD à se connecter à votre application.

Vous devez mettre à jour la liste des émetteurs de jetons valides et restreindre l’accès à une liste spécifique d’utilisateurs locataires Azure AD qui peuvent se connecter.

Pour obtenir les valeurs correspondantes, examinez les métadonnées de découverte OpenID Connect de chacun des locataires Azure AD à partir desquels les utilisateurs seront autorisés à se connecter. Le format de l’URL des métadonnées est semblable à `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, où `your-tenant` est votre nom de locataire Azure AD. Par exemple :

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Effectuez ces étapes pour chaque locataire Azure AD qui doit être utilisé pour se connecter :

1. Ouvrez votre navigateur et accédez à l’URL des métadonnées OpenID Connect pour le locataire. Recherchez l’objet **issuer** et enregistrez sa valeur. Elle doit ressembler à `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Copiez et collez la valeur dans la clé **ValidTokenIssuerPrefixes**. Séparez plusieurs émetteurs par une virgule. Un exemple avec deux émetteurs apparaît dans l’exemple XML `ClaimsProvider` précédent.

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec vos annuaires Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Sélectionnez **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour le rendre disponible, vous devez créer un doublon d’un modèle de parcours utilisateur existant et le modifier afin qu’il dispose également du fournisseur d’identité Azure AD.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInContoso`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** à Azure AD, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé dans *TrustFrameworkExtensions.xml*.
1. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `AzureADExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Associez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’**ID** la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `Common-AAD`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue par le biais d’une application que vous inscrivez dans votre locataire B2C. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé :

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, attribuez-lui le nouveau nom *SignUpSignInADFS.xml*.
1. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInContoso`.
1. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Par exemple : `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Mettez à jour la valeur de l’attribut **ReferenceId** dans **DefaultUserJourney** pour qu’elle corresponde à l’ID du parcours utilisateur que vous avez créé précédemment. Par exemple, *SignUpSignInContoso*.
1. Enregistrez vos modifications et téléchargez le fichier.
1. Sous **Stratégies personnalisées**, sélectionnez la nouvelle stratégie dans la liste.
1. Dans la liste déroulante **Sélectionner une application**, sélectionnez l’application Azure AD B2C que vous avez créée précédemment. Par exemple, *testapp1*.
1. Copiez le point de terminaison **Exécuter maintenant** et ouvrez-le dans une fenêtre de navigation privée, par exemple en mode navigation privée dans Google Chrome ou dans une fenêtre InPrivate dans Microsoft Edge. L’ouverture dans une fenêtre de navigation privée vous permet de tester le parcours utilisateur complet en n’utilisant pas les informations d’identification Azure AD actuellement mises en cache.
1. Sélectionnez le bouton de connexion Azure AD, par exemple *Contoso Employee* (Employé Contoso), puis entrez les informations d’identification d’un utilisateur dans l’un de vos locataires d’organisation Azure AD. Vous êtes invité à autoriser l’application, puis à entrer des informations pour votre profil.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

Pour tester la fonctionnalité de connexion multilocataire, effectuez les deux dernières étapes à l’aide des informations d’identification d’un utilisateur qui existe dans un autre locataire Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous utilisez des stratégies personnalisées, vous pouvez parfois avoir besoin d’informations supplémentaires lors du dépannage d’une stratégie pendant son développement.

Pour faciliter le diagnostic des problèmes, vous pouvez placer temporairement la stratégie en « mode développeur » et collecter des journaux avec Azure Application Insights. Pour savoir comment procéder, consultez [Azure Active Directory B2C : collecte des journaux](troubleshoot-with-application-insights.md).
