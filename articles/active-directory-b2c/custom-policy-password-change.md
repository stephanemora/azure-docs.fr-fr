---
title: Configurer la modification du mot de passe à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment permettre aux utilisateurs de modifier leur mot de passe à l’aide de stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6bb4b762fead279bcc8492cb902e2059d7cfc68c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851076"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans Azure Active Directory B2C (Azure AD B2C), vous pouvez permettre aux utilisateurs qui sont connectés avec un compte local de modifier leur mot de passe sans avoir à prouver leur authenticité avec une vérification par e-mail. Si la session expire avant que l’utilisateur ne parvienne au flux du changement de mot de passe, il est invité à se reconnecter. Cet article vous montre comment configurer la modification du mot de passe dans des [stratégies personnalisées](custom-policy-overview.md). Il est également possible de configurer la [réinitialisation du mot de passe en libre-service](user-flow-self-service-password-reset.md) pour les flux d’utilisateurs.

## <a name="prerequisites"></a>Conditions préalables requises

Suivez les étapes dans [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Ajouter les éléments

1. Ouvrez votre fichier *TrustframeworkExtensions.xml* et ajoutez l’élément **ClaimType** avec l’identificateur `oldPassword` à l’élément [ClaimsSchema](claimsschema.md) :

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Un élément [ClaimsProvider](claimsproviders.md) contient le profil technique qui authentifie l’utilisateur. Ajoutez les fournisseurs de revendications suivants à l’élément **ClaimsProviders** :

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Remplacez `IdentityExperienceFrameworkAppId` par l’ID de l’application IdentityExperienceFramework que vous avez créée dans le tutoriel des prérequis. Remplacez `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application ProxyIdentityExperienceFramework que vous avez aussi créée précédemment.

3. L’élément [UserJourney](userjourneys.md) définit le chemin emprunté par l’utilisateur lors de l’interaction avec votre application. Ajoutez l’élément **UserJourneys** s’il n’existe pas avec l’élément **UserJourney** identifié comme `PasswordChange` :

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Enregistrez le fichier de stratégie *TrustframeworkExtensions.xml*.
5. Copiez le fichier *ProfileEdit.xml* que vous avez téléchargé avec le pack de démarrage et nommez-le *ProfileEditPasswordChange.xml*.
6. Ouvrez le nouveau fichier, puis mettez à jour l’attribut **PolicyId** avec une valeur unique. Cette valeur est le nom de votre stratégie. Par exemple, *B2C_1A_profile_edit_password_change*.
7. Modifiez l’attribut **ReferenceId** dans `<DefaultUserJourney>` pour qu’il corresponde à l’ID du parcours utilisateur que vous avez créé. Par exemple, *PasswordChange*.
8. Enregistrez vos modifications.

Vous pouvez trouver l’exemple de stratégie [ici](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Tester votre stratégie

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

### <a name="upload-the-files"></a>Téléchargement des fichiers

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Dans la page Stratégies personnalisées, cliquez sur **Charger une stratégie**.
6. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustframeworkExtensions.xml*.
7. Cliquez sur **Télécharger**.
8. Répétez les étapes 5 à 7 pour le fichier de la partie de confiance (par exemple, *ProfileEditPasswordChange.xml*).

### <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie que vous avez changée. Par exemple, *B2C_1A_profile_edit_password_change*.
2. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton, l’**URL de réponse** doit indiquer `https://jwt.ms`.
3. Cliquez sur **Exécuter maintenant**. Connectez-vous avec le compte que vous avez créé précédemment. Vous devez à présent avoir la possibilité de modifier le mot de passe.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer la complexité du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-password-complexity.md).
