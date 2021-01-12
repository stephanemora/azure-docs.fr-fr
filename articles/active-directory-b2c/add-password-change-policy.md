---
title: Configurer la modification du mot de passe à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment permettre aux utilisateurs de modifier leur mot de passe à l’aide de stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629122"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans Azure Active Directory B2C (Azure AD B2C), vous pouvez permettre aux utilisateurs qui sont connectés avec un compte local de modifier leur mot de passe sans avoir à prouver leur authenticité avec une vérification par e-mail. Le flux de changement de mot de passe implique les étapes suivantes :

1. Connectez-vous avec un compte local. Si la session est toujours active, Azure AD B2C autorise l’utilisateur et passe à l’étape suivante.
1. Les utilisateurs doivent vérifier l’**ancien mot de passe**, créer et confirmer le **nouveau mot de passe**.

![Flux de changement de mot de passe](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>Conditions préalables requises

* Suivez les étapes dans [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).
* Si ce n’est pas déjà fait, [inscrivez une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Ajouter les éléments

1. Ouvrez votre fichier *TrustframeworkExtensions.xml* et ajoutez l’élément **ClaimType** avec l’identificateur `oldPassword` à l’élément [ClaimsSchema](claimsschema.md) :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Un élément [ClaimsProvider](claimsproviders.md) contient le profil technique qui authentifie l’utilisateur. Ajoutez les fournisseurs de revendications suivants à l’élément **ClaimsProviders** :

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. L’élément [UserJourney](userjourneys.md) définit le chemin emprunté par l’utilisateur lors de l’interaction avec votre application. Ajoutez l’élément **UserJourneys** s’il n’existe pas avec l’élément **UserJourney** identifié comme `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
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

## <a name="upload-and-test-the-policy"></a>Chargez et testez la stratégie.

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

- Recherchez l’exemple de stratégie dans [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Découvrez comment vous pouvez [configurer la complexité du mot de passe dans Azure AD B2C](password-complexity.md).
- Configurez un [flux de réinitialisation de mot de passe](add-password-reset-policy.md).

::: zone-end
