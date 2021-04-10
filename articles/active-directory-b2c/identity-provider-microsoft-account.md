---
title: Configurer l’inscription et la connexion avec un compte Microsoft
titleSuffix: Azure AD B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes Microsoft dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 12e8222ad59aae31baa7a549519c97550b711703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579941"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft

Pour autoriser la connexion des utilisateurs avec un compte Microsoft dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Inscrire une application auprès de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md). Si vous n’avez pas encore de compte Microsoft, vous pouvez en créer un sur [https://www.live.com/](https://www.live.com/).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple, *MSAapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel (tout annuaire Azure AD mutualisé) et comptes Microsoft personnels (par exemple, Skype, Xbox)** .

   Pour plus d’informations sur les différents types de compte disponibles, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Pour **URI de redirection (facultatif)** , sélectionnez **Web** et entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé.
1. Sélectionnez **Inscrire**.
1. Enregistrez l’**ID d’application (client)** indiqué dans la page Vue d’ensemble de l’application. Vous avez besoin de l’ID client quand vous configurez le fournisseur d’identité dans la section suivante.
1. Sélectionnez **Certificats et secrets**.
1. Cliquez sur **Nouveau secret client**.
1. Entrez une **Description** pour le secret, par exemple *Mot de passe 1 de l’application*, puis cliquez sur **Ajouter**.
1. Enregistrez le mot de passe de l’application affiché dans la colonne **Valeur**. Vous avez besoin de la clé secrète client quand vous configurez le fournisseur d’identité dans la section suivante.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Configurer Microsoft en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Compte Microsoft**.
1. Saisissez un **Nom**. Par exemple, *MSA*.
1. Dans **ID client**, entrez l’ID (client) de l’application Azure AD que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Ajoutez un fournisseur d’identité Microsoft à un workflow d’utilisateur 

À ce stade, le fournisseur d’identité Microsoft a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Pour ajouter un fournisseur d’identité Microsoft à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur à ajouter au fournisseur d’identité Microsoft.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Compte Microsoft**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux utilisateur**.
1. Sur la page d'inscription ou de connexion, sélectionnez **Microsoft** pour vous connecter avec un compte Microsoft.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

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

Maintenant que vous avez créé l’application dans votre locataire Azure AD, vous devez stocker la clé secrète client de cette application dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `MSASecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez notée dans la section précédente.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Cliquez sur **Créer**.

## <a name="configure-microsoft-as-an-identity-provider"></a>Configurer Microsoft en tant que fournisseur d’identité

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Microsoft, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure Active Directory B2C peut communiquer grâce à un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant l’élément **ClaimsProvider** au fichier d’extension de votre stratégie.

1. Ouvrez le fichier de stratégie *TrustframeworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Sur la page d'inscription ou de connexion, sélectionnez **Microsoft** pour vous connecter avec un compte Microsoft.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end
