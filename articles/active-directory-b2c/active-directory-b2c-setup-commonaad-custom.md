---
title: Ajouter un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: Ajoutez un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées - Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68eab85c7f67ad3af18c6066c29e1250e1be3d23
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344404"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : autoriser la connexion d’utilisateurs à un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à l’aide du point de terminaison multi-locataire pour Azure Active Directory (Azure AD) en utilisant des [stratégies personnalisées](active-directory-b2c-overview-custom.md). Cela permet aux utilisateurs issus de différents locataires Azure AD de se connecter à Azure AD B2C sans configurer un fournisseur technique pour chaque locataire. Toutefois, les membres invités dans ces locataires **ne pourront pas** se connecter. Il faudra pour cela [configurer individuellement chaque locataire](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Nous utilisons « contoso.com » pour l’organisation (locataire) Azure AD et « fabrikamb2c.onmicrosoft.com » comme locataire Azure AD B2C dans les instructions suivantes.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :
     
1. Création d’un locataire Azure Active Directory B2C (Azure AD B2C).
1. Création d’une application Azure AD B2C.    
1. Inscription de deux applications de moteur de stratégie.  
1. Configuration des clés. 
1. Configuration du pack de démarrage.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Étape 1. Créer une application Azure AD mutualisée

Pour autoriser la connexion d’utilisateurs à l’aide du point de terminaison Azure AD multi-locataire, il vous faut une application multi-locataire inscrite dans l’un de vos locataires Azure AD. Dans cet article, nous allons vous expliquer comment créer une application Azure AD mutualisée dans votre locataire Azure AD B2C. Puis vous découvrirez comment autoriser la connexion d’utilisateurs par le biais de cette application Azure AD mutualisée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu supérieur, sélectionnez votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD B2C dans lequel inscrire l’application Azure AD (fabrikamb2c.onmicrosoft.com).
1. Cliquez sur **Autres services** dans le volet gauche, puis recherchez « Inscriptions d’applications ».
1. Sélectionnez **Nouvelle inscription d’application**.
1. Entrez un nom pour votre application (par exemple, `Azure AD B2C App`).
1. Pour le type d’application, sélectionnez **Application web/API**.
1. Pour **URL de connexion**, entrez l’URL suivante où `yourtenant` est remplacé par le nom de votre locataire Azure AD B2C (`fabrikamb2c.onmicrosoft.com`) :

    >[!NOTE]
    >La valeur de « yourtenant » doit être en minuscules dans **l’URL de connexion**.

    ```
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Enregistrez l’ID de l’application.
1. Sélectionnez l’application que vous venez de créer.
1. Sous le panneau **Paramètres**, sélectionnez **Propriétés**.
1. Définissez **Mutualisé** sur **Oui**.
1. Dans le panneau **Paramètres**, sélectionnez **Clés**.
1. Créez une clé et enregistrez-la. Vous allez l’utiliser dans les étapes de la prochaine section.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Étape 2. Ajouter la clé d’Azure AD à Azure AD B2C

Vous devez inscrire la clé d’application dans les paramètres Azure AD B2C. Pour ce faire :

1. Accédez au menu de paramètres d’Azure AD B2C.
1. Cliquez sur **Infrastructure d’expérience d’identité** > **Clés de stratégie**.
1. Sélectionnez **+Ajouter**.
1. Sélectionnez ou entrez les options suivantes :
   * Sélectionnez **Manuel**.
   * Pour **Nom**, choisissez un nom correspondant au nom de votre locataire Azure AD (par exemple, `AADAppSecret`).  Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
   * Collez votre clé d’application dans la zone **Secret**.
   * Sélectionnez **Signature**.
1. Sélectionnez **Créer**.
1. Vérifiez que vous avez créé la clé `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Étape 3. Ajoutez un fournisseur de revendications dans votre stratégie de base

Si vous souhaitez que les utilisateurs se connectent à l’aide d’Azure AD, vous devez définir Azure AD comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD au nœud `<ClaimsProvider>` dans le fichier d’extension de votre stratégie :

1. Ouvrez le fichier d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail.
1. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, ajoutez-la sous le nœud racine.
1. Ajoutez un nœud `<ClaimsProvider>` comme suit :

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. Sous le nœud `<ClaimsProvider>`, mettez à jour la valeur de `<Domain>` sur une valeur unique utilisable pour la distinguer d’autres fournisseurs d’identité.
1. Sous le nœud `<TechnicalProfile>`, mettez à jour la valeur de `<DisplayName>`. Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion.
1. Mettez à jour la valeur pour `<Description>`.
1. Définissez `<Item Key="client_id">` sur l’ID d’application issu de l’inscription de l’application mutualisée Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Étape 3.1 Restreindre l’accès à une liste spécifique de locataires Azure AD

> [!NOTE]
> La valeur `https://sts.windows.net` pour **ValidTokenIssuerPrefixes** autorise TOUS les utilisateurs Azure AD à se connecter à votre application.

Vous devez mettre à jour la liste d’émetteurs de jetons valides et restreindre l’accès à une liste spécifique de locataires Azure AD autorisés pour la connexion des utilisateurs. Pour obtenir les valeurs correspondantes, vous devrez examiner les métadonnées de chacun des locataires Azure AD spécifiques à partir desquels les utilisateurs seront autorisés à se connecter. Le format des données présente l’aspect suivant : `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, où `yourAzureADtenant` est le nom de votre locataire Azure AD (contoso.com ou tout autre locataire Azure AD).
1. Ouvrez votre navigateur et accédez à l’URL des métadonnées.
1. Dans le navigateur, recherchez l’objet « issuer » et copiez sa valeur. Elle doit ressembler à ceci : `https://sts.windows.net/{tenantId}/`.
1. Collez la valeur de la clé `ValidTokenIssuerPrefixes`. Vous pouvez ajouter plusieurs valeurs en les séparant par une virgule. Un exemple de cette opération est commenté dans l’exemple de code XML ci-dessus.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Étape 4. Inscrire le fournisseur de revendications de compte Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Étape 4.1 : Effectuer une copie du parcours utilisateur

Vous devez maintenant ajouter le fournisseur d’identité Azure AD à l’un de vos parcours utilisateur. À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion.

Pour changer cela, nous créons un doublon d’un modèle de parcours utilisateur et le modifions afin qu’il dispose également du fournisseur d’identité Azure AD :

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
1. Recherchez l’élément `<UserJourneys>` et copiez l’ensemble du nœud `<UserJourney>` qui inclut `Id="SignUpOrSignIn"`.
1. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité du nœud `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.
1. Renommez l’ID du nouveau parcours utilisateur (par exemple, en `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Étape 4.2 : Afficher le « bouton »

L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur un écran d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` pour Azure AD, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément :

1. Recherchez le nœud `<OrchestrationStep>` comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Définissez `TargetClaimsExchangeId` sur une valeur appropriée. Nous vous recommandons de suivre la même convention que pour les autres : *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Étape 4.3 : Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD :

1. Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Mettez à jour `Id` sur la même valeur que celle de `TargetClaimsExchangeId` dans la section précédente.
1. Mettez à jour `TechnicalProfileReferenceId` avec l’ID du profil technique précédemment créé (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Étape 5. Créer une stratégie de partie de confiance

Vous devez maintenant mettre à jour le fichier de partie de confiance qui initialisera le parcours utilisateur que vous venez de créer :
 
1. Effectuez une copie de SignUpOrSignIn.xml dans votre répertoire de travail et renommez-le (par exemple, SignUpOrSignInWithAAD.xml).  
1. Ouvrez le nouveau fichier et mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique (par exemple, SignUpOrSignInWithAAD). Celle-ci correspond au nom de votre stratégie (par exemple, B2C\_1A\_SignUpOrSignInWithAAD). 
1. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour qu’il corresponde à l’ID du parcours utilisateur que vous avez créé (SignUpOrSignUsingAzureAD). 
1. Enregistrez vos modifications et chargez le fichier. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Étape 6 : Charger la stratégie sur votre locataire

1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), puis sélectionnez **Azure AD B2C**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Toutes les stratégies**.
1. Sélectionnez **Charger la stratégie**.
1. Activez la case à cocher **Remplacer la stratégie si elle existe**.
1. Chargez le fichier `TrustFrameworkExtensions.xml` et le fichier de partie de confiance (par exemple `SignUpOrSignInWithAAD.xml`) et assurez-vous que leur validation réussit.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Étape 7 : Tester la stratégie personnalisée en utilisant Exécuter maintenant

1. Sélectionnez **Paramètres Azure AD B2C**, puis **Infrastructure d’expérience d’identité**.
    > [!NOTE]
    > L’option Exécuter maintenant nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

1. Ouvrez la stratégie personnalisée de partie de confiance que vous avez chargée (*B2C\_1A\_SignUpOrSignInWithAAD*), puis sélectionnez **Exécuter maintenant**.
1. Vous devriez à présent être en mesure de vous connecter à l’aide du compte Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Facultatif) Étape 8. Inscrire le fournisseur de revendications de compte Azure AD au parcours utilisateur de modification de profil

Vous voudrez peut-être également ajouter le fournisseur d’identité de compte Azure AD à votre parcours utilisateur `ProfileEdit`. Pour que le parcours utilisateur soit disponible, répétez les étapes 4 à 6. Cette fois-ci, sélectionnez le nœud `<UserJourney>` qui contient `Id="ProfileEdit"`. Enregistrez, chargez et testez votre stratégie.

## <a name="troubleshooting"></a>Résolution de problèmes

Pour diagnostiquer des problèmes, consultez la page sur la [résolution des problèmes](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Étapes suivantes

Envoyez vos commentaires à l’adresse [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
