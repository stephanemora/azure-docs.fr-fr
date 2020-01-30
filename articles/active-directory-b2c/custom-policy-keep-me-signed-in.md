---
title: Maintenir la connexion dans Azure Active Directory B2C
description: Découvrez comment configurer la fonctionnalité « Maintenir la connexion » dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 052e1bc4e9a14b34e21b0bfeb4193fbd0b2b1a22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851064"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Activer la fonctionnalité « Maintenir la connexion » dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez activer la fonctionnalité Maintenir la connexion pour les utilisateurs de vos applications web et native qui ont des comptes locaux dans votre annuaire Azure AD B2C (Azure Active Directory B2C). Cette fonctionnalité accorde l’accès aux utilisateurs qui retournent dans votre application sans avoir à entrer une nouvelle fois leur nom d’utilisateur et leur mot de passe. Cet accès est révoqué lorsque l’utilisateur se déconnecte.

Vous ne devez pas activer cette option sur les ordinateurs publics.

![Exemple de page de connexion avec la case à cocher Maintenir la connexion](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Conditions préalables requises

Un locataire Azure AD B2C configuré pour permettre la connexion à un compte local. La fonctionnalité Maintenir la connexion n’est pas prise en charge pour les comptes des fournisseurs d’identité externes.

Si vous n’avez pas de locataire, vous pouvez en créer un en suivant les étapes décrites du [Tutoriel : Créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Ajouter un élément de définition de contenu

Sous l’élément **BuildingBlocks** de votre fichier d’extension, ajoutez un élément **ContentDefinitions**.

1. Sous l’élément **ContentDefinitions**, ajoutez un élément **ContentDefinition** avec un identificateur `api.signuporsigninwithkmsi`.
2. Sous l’élément **ContentDefinition**, ajoutez les éléments **LoadUri**, **RecoveryUri** et **DataUri**. La valeur `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` de l’élément **DataUri** est un identificateur compréhensible par une machine qui affiche une case à cocher « Maintenir la connexion » sur les pages de connexion. Cette valeur ne doit pas être modifiée.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Ajouter un fournisseur de revendications de connexion à un compte local

Vous pouvez définir la connexion du compte local comme un fournisseur de revendications à l’aide de l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie :

1. Ouvrez le fichier *TrustFrameworkExtensions.xml* dans votre répertoire de travail.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine. Le [pack de démarrage](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) comprend un fournisseur de revendications de connexion de compte local.
3. Ajoutez un élément **ClaimsProvider** avec le **DisplayName** et le **TechnicalProfile**, comme indiqué dans l’exemple suivant :

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Ajouter les identificateurs d’application à votre stratégie personnalisée

Ajoutez les identificateurs d’application au fichier *TrustFrameworkExtensions.xml*.

1. Dans le fichier *TrustFrameworkExtensions.xml*, recherchez l’élément **TechnicalProfile** avec l’identificateur `login-NonInteractive`, et l’élément **TechnicalProfile** avec l’identificateur`login-NonInteractive-PasswordChange`, puis remplacez toutes les valeurs `IdentityExperienceFrameworkAppId` par l’identificateur de l’application Identity Experience Framework, comme décrit dans [Bien démarrer](custom-policy-get-started.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Remplacez toutes les valeurs `ProxyIdentityExperienceFrameworkAppId` par l’identificateur de l’application Proxy Identity Experience Framework, comme décrit dans [Bien démarrer](custom-policy-get-started.md).
3. Enregistrez le fichier d’extensions.

## <a name="create-a-kmsi-enabled-user-journey"></a>Créer un parcours utilisateur avec une option Maintenir la connexion

Ajoutez à votre parcours utilisateur le fournisseur de revendications de connexion pour un compte local.

1. Ouvrez le fichier de base de votre stratégie. Par exemple, ouvrez *TrustFrameworkBase.xml*.
2. Recherchez l’élément **UserJourneys**, puis copiez l’intégralité du contenu de l’élément **UserJourney** qui utilise l’identificateur `SignUpOrSignIn`.
3. Ouvrez le fichier d’extension. Par exemple, ouvrez *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Modifiez la valeur de l’identificateur pour le nouveau parcours utilisateur. Par exemple : `SignUpOrSignInWithKmsi`.
6. Enfin, dans la première étape d’orchestration, remplacez la valeur **ContentDefinitionReferenceId** par `api.signuporsigninwithkmsi`. Lorsque vous définissez cette valeur, la case du parcours utilisateur est cochée.
7. Enregistrez et chargez le fichier d’extension, puis vérifiez que toutes les validations ont réussi.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Créer un fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie du fichier *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, *SignUpOrSignInWithKmsi.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Il s’agit du nom de votre stratégie. Par exemple : `SignUpOrSignInWithKmsi`.
3. Remplacez l’attribut **ReferenceId** par l’élément **DefaultUserJourney** pour correspondre à l’identificateur du nouveau parcours utilisateur que vous avez créé. Par exemple : `SignUpOrSignInWithKmsi`.

    KMSI est configuré à l'aide de l'élément **UserJourneyBehaviors** avec **SingleSignOn**, **SessionExpiryType** et **SessionExpiryInSeconds** comme premiers éléments enfants. L’attribut **KeepAliveInDays** contrôle la durée pendant laquelle l’utilisateur reste connecté. Dans l’exemple suivant, la session Maintenir la connexion expire automatiquement après `7` jours, quelle que soit la fréquence à laquelle l’utilisateur effectue une authentification silencieuse. Si vous définissez la valeur **KeepAliveInDays** sur `0`, vous désactivez la fonctionnalité Maintenir la connexion. Par défaut, cette valeur est définie sur `0`. Si la valeur de **SessionExpiryType** est `Rolling`, la session Maintenir la connexion est prolongée de `7` jours chaque fois que l’utilisateur effectue une authentification silencieuse.  Si `Rolling` est sélectionné, le nombre de jours doit être minimal.

    La valeur de **SessionExpiryInSeconds** correspond au délai d’expiration d’une session SSO. Elle est utilisée en interne par Azure AD B2C pour vérifier si la session Maintenir la connexion a expiré ou non. La valeur de **KeepAliveInDays** détermine la valeur d’expiration ou d’âge maximal du cookie d’authentification unique dans le navigateur web. Contrairement à **SessionExpiryInSeconds**, **KeepAliveInDays** est utilisé pour empêcher le navigateur d’effacer le cookie au moment de sa fermeture. Un utilisateur peut se connecter en mode silencieux uniquement en présence d’un cookie de session SSO, ce qui est contrôlé par **KeepAliveInDays**, et uniquement s’il n’a pas expiré, ce qui est contrôlé par **SessionExpiryInSeconds**.

    Si un utilisateur n’active pas **Maintenir la connexion** dans la page d’inscription et de connexion, une session expire au bout du délai indiqué par **SessionExpiryInSeconds** ou quand le navigateur est fermé. Si un utilisateur active **Maintenir la connexion**, la valeur de **KeepAliveInDays** remplace la valeur de **SessionExpiryInSeconds** et détermine le délai d’expiration de la session. Même si les utilisateurs ferment et rouvrent le navigateur, ils peuvent toujours se connecter en mode silencieux à condition de le faire dans le temps imparti par **KeepAliveInDays**. Nous vous recommandons de définir la valeur de **SessionExpiryInSeconds** sur une courte période (1 200 secondes), tandis que vous pouvez définir la valeur de **KeepAliveInDays** sur une période relativement longue (7 jours), comme l’illustre l’exemple suivant :

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Enregistrez vos modifications, puis chargez le fichier.
5. Pour tester la stratégie personnalisée que vous avez chargée, dans le portail Azure, accédez à la page Stratégie, puis sélectionnez **Exécuter maintenant**.

Vous pouvez trouver l’exemple de stratégie [ici](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
