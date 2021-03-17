---
title: Configurer la connexion avec un fournisseur SAML Salesforce à l’aide du protocole SAML
titleSuffix: Azure AD B2C
description: Configurez la connexion avec un fournisseur SAML Salesforce à l’aide du protocole SAML dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 63288bca124959463dc6ea16cb9d681c68ad00da
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448187"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Configurer la connexion avec un fournisseur SAML Salesforce à l’aide du protocole SAML dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment activer la connexion d’utilisateurs à partir d’une organisation Salesforce à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C). Pour activer la connexion, vous devez ajouter un [fournisseur d'identité SAML](identity-provider-generic-saml.md) à une stratégie personnalisée.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Si vous n’avez pas déjà fait, inscrivez-vous pour un [compte édition développeur gratuit](https://developer.salesforce.com/signup). Cet article utilise le [Lightning Experience de Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- Vous avez déjà [configuré un domaine My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pour votre organisation Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Configurer Salesforce en tant que fournisseur d’identité

1. [Connectez-vous à Salesforce](https://login.salesforce.com/).
2. Dans le menu de gauche, sous **Paramètres**, développez **Identité**, puis sélectionnez **Fournisseur d’identité**.
3. Cliquez sur **Activer le fournisseur d’identité**.
4. Sous **Select the certificate (Sélectionner le certificat)** , sélectionnez le certificat que vous souhaitez que Salesforce utilise pour communiquer avec Azure AD B2C Vous pouvez utiliser le certificat par défaut.
5. Cliquez sur **Enregistrer**.

### <a name="create-a-connected-app-in-salesforce"></a>Créer une application connectée dans Salesforce

1. Sur la page **Fournisseur d’identité**, sélectionnez **Les fournisseurs de services sont maintenant créés via des applications connectées. Cliquez ici.**
2. Sous **Basic Information (Informations de base)** , entrez les valeurs requises pour votre application connectée.
3. Sous **Paramètres de l’application web**, cochez la case **Activer SAML**.
4. Dans le champ **Entity ID (ID d’entité)** , entrez l’URL suivante. Assurez-vous de remplacer la valeur de `your-tenant` par le nom de votre locataire Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Dans le champ **ACS URL (URL ACS)** , entrez l’URL suivante. Assurez-vous de remplacer la valeur de `your-tenant` par le nom de votre locataire Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Faites défiler vers le bas de la liste, puis cliquez sur **Enregistrer**.

### <a name="get-the-metadata-url"></a>Obtenir l’URL des métadonnées

1. Dans la page de présentation de votre application connectée, cliquez sur **Manage (Gérer)** .
2. Copiez la valeur de **point de terminaison de découverte des métadonnées**, puis enregistrez-la. Vous l’utiliserez plus loin dans cet article.

### <a name="set-up-salesforce-users-to-federate"></a>Configurer des utilisateurs Salesforce pour fédérer

1. Dans la page **Gérer** de votre application connectée, cliquez sur **Gérer les profils**.
2. Sélectionnez les profils (ou groupes d’utilisateurs) que vous souhaitez fédérer avec Azure AD B2C. En tant qu’un administrateur système, activez la case à cocher **Administrateur système** afin de pouvoir fédérer à l’aide de votre compte Salesforce.

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez enregistrer le certificat que vous avez créé dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Upload`.
7. Entrez un **nom** pour la stratégie. Par exemple, SAMLSigningCert. Le préfixe `B2C_1A_` est automatiquement ajouté au nom de votre clé.
8. Recherchez et sélectionnez le certificat B2CSigningCert.pfx que vous avez créé.
9. Entrez le **mot de passe** du certificat.
3. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Salesforce, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Salesforce en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie. Pour plus d'informations, consultez [Définir un fournisseur d'identité SAML](identity-provider-generic-saml.md).

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Mettez à jour la valeur de **PartnerEntity** avec l’URL des métadonnées de Salesforce que vous avez copiée précédemment.
1. Mettez à jour la valeur des deux instances de **StorageReferenceId** avec le nom de la clé de votre certificat de signature. Par exemple, B2C_1A_SAMLSigningCert.
1. Recherchez la section `<ClaimsProviders>` et ajoutez l’extrait de code XML suivant. Si votre stratégie contient déjà le profil technique `SM-Saml-idp`, passez à l’étape suivante. Pour plus d’informations, voir [Gestion de sessions d’authentification unique](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Sur la page d'inscription ou de connexion, sélectionnez **Salesforce** pour vous connecter avec un compte Salesforce.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end