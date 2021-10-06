---
title: Configurer la modification du mot de passe à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment configurer une stratégie personnalisée afin que les utilisateurs puissent modifier leur mot de passe dans le Répertoire actif Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 16295eb707968a606c74813f9f6b7585aaf59546
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570618"
---
# <a name="set-up-password-change-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la modification du mot de passe avec des stratégies personnalisées dans le Répertoire actif Azure B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Vous pouvez configurer le Répertoire actif Azure B2C (Azure AD B2C) afin qu’un utilisateur connecté avec un compte local puisse modifier son mot de passe sans utiliser la vérification par e-mail pour prouver son identité. 

Le flux de changement de mot de passe implique les étapes suivantes :

1. L’utilisateur se connecte à son compte local. Si la session est toujours active, Azure AD B2C autorise l’utilisateur et passe à l’étape suivante.
1. Dans l'**Ancien mot de passe**, l’utilisateur vérifie son ancien mot de passe. Dans le **Nouveau mot de passe**, il crée et confirme son nouveau mot de passe.

   ![Capture d’écran montrant deux boîtes de dialogue numérotées pour la modification du mot de passe.](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Un utilisateur peut utiliser le flux de modification de mot de passe décrit dans cet article uniquement lorsqu’il connaît son mot de passe et qu’il souhaite modifier son mot de passe. Nous vous recommandons également d’activer la [réinitialisation de mot de passe libre-service](add-password-reset-policy.md) pour prendre en charge les cas où l’utilisateur oublie son mot de passe.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prérequis

* Suivez les étapes dans [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
* [Inscrire une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Ajouter les éléments

1. Ouvrez votre fichier *TrustFrameworkExtensions.xml*. Ajoutez l’élément **ClaimType** suivant à l’élément [ClaimsSchema](claimsschema.md), avec un identificateur `oldPassword` :

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

1. Un élément [ClaimsProvider](claimsproviders.md) contient le profil technique qui authentifie l’utilisateur. Ajoutez les fournisseurs de revendications suivants à l’élément **ClaimsProviders** :

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

1. L’élément [UserJourneys](userjourneys.md) définit le chemin d’accès que l’utilisateur utilise lorsqu’il interagit avec votre application. Ajoutez l’élément **UserJourneys** s’il n’existe pas, avec **l’identificateur** UserJourney`PasswordChange` :

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

1. Enregistrez le fichier de stratégie *TrustframeworkExtensions.xml*.
1. Copiez le fichier *ProfileEdit.xml* que vous avez téléchargé avec le pack de démarrage et nommez-le *ProfileEditPasswordChange.xml*.
1. Ouvrez le nouveau fichier, puis mettez à jour l’attribut **PolicyId** avec une valeur unique. Cette valeur est le nom de votre stratégie. Par exemple, *B2C_1A_profile_edit_password_change*.
1. Modifiez l’attribut **ReferenceId** dans **DefaultUserJourney** afin qu’il corresponde à l’ID du nouveau parcours utilisateur que vous avez créé. Par exemple, *PasswordChange*.
1. Enregistrez vos modifications.

## <a name="upload-and-test-the-policy"></a>Chargez et testez la stratégie.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD B2C en sélectionnant l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Dans **Stratégies personnalisées**, sélectionnez **Télécharger la stratégie**.
1. Sélectionnez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
1. Sélectionnez **Charger**.
1. Répétez les étapes 5 à 7 pour le fichier de la partie de confiance (par exemple, *ProfileEditPasswordChange.xml*).

## <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie que vous avez changée. Par exemple, *B2C_1A_profile_edit_password_change*.
1. Pour **Application**, sélectionnez l’application que vous avez inscrite précédemment. Pour voir le jeton, l’**URL de réponse** doit indiquer `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant**. Dans le nouvel onglet qui s’ouvre, supprimez « &prompt=login » de l’URL, puis actualisez l’onglet. Ensuite, connectez-vous avec le compte que vous avez créé précédemment. Une boîte de dialogue de modification de mot de passe vous donne la possibilité de modifier le mot de passe.

## <a name="next-steps"></a>Étapes suivantes

* Recherchez l’[exemple de stratégie dans GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
* Découvrez comment vous pouvez [configurer la complexité du mot de passe dans Azure AD B2C](password-complexity.md).
* Configurez un [flux de réinitialisation de mot de passe](add-password-reset-policy.md).

::: zone-end
