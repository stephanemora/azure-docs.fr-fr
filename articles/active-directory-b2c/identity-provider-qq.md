---
title: Configurer l’inscription et la connexion avec un compte QQ à l’aide d’Azure Active Directory B2C
description: Proposez l’inscription et la connexion aux clients disposant de comptes QQ dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8bc2cddf4d0380e5dc22e8250b6ee26f4d005b8a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952425"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte QQ à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>Créer une application QQ

Pour autoriser la connexion des utilisateurs avec un compte QQ dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans le [portail des développeurs QQ](http://open.qq.com). Si vous n’avez pas encore de compte QQ, vous pouvez en souscrire un à l’adresse [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Inscrivez-vous au programme de développement QQ

1. Connectez-vous au [portail des développeurs QQ](http://open.qq.com) avec les informations d’identification de votre compte QQ.
1. Une fois connecté, accédez à [https://open.qq.com/reg](https://open.qq.com/reg) vous inscrire en tant que développeur.
1. Sélectionnez **个人** (développeur individuel).
1. Entrez les informations requises et sélectionnez **下一步** (étape suivante).
1. Finalisez le processus de vérification d’e-mail. Une fois que vous êtes inscrit en tant que développeur, vous devez attendre votre approbation pendant quelques jours.

### <a name="register-a-qq-application"></a>Inscrire une application QQ

1. Accédez à [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Sélectionnez **应用管理** (gestion des applications).
1. Sélectionnez **创建应用**(créer une application) et entrez les informations requises.
1. Dans le champ **授权回调域** (URL de rappel), entrez `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Par exemple, si votre `tenant_name` est contoso, définissez l’URL sur `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Sélectionnez **创建应用** (créer une application).
1. Dans la page de confirmation, sélectionnez **应用管理** (gestion des applications) pour revenir à la page de gestion des applications.
1. Sélectionnez **查看** (afficher) en regard de l’application que vous avez créée.
1. Sélectionnez **修改**(modifier).
1. Copiez l’**ID de l’application** et la **clé de l’application**. Vous avez besoin de ces deux valeurs pour ajouter le fournisseur d’identité à votre locataire.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>Configuration de QQ en tant que fournisseur d'identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **QQ (Préversion)**.
1. Saisissez un **Nom**. Par exemple, *QQ*.
1. Dans **ID client**, entrez l’ID d’application de l’application QQ que vous avez créée précédemment.
1. Dans **Clé secrète client**, entrez la clé d’application que vous avez enregistrée.
1. Sélectionnez **Enregistrer**.

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité QQ à un flux d’utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité QQ.
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **QQ**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Manual`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `QQSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
9. Pour **Utilisation de la clé**, sélectionnez `Signature`.
10. Cliquez sur **Créer**.

## <a name="configure-qq-as-an-identity-provider"></a>Configuration de QQ en tant que fournisseur d'identité

Pour permettre aux utilisateurs de se connecter avec un compte QQ, vous devez définir le compte comme fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte QQ en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
