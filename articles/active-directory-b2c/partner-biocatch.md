---
title: Tutoriel pour configurer BioCatch Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Azure Active Directory B2C avec BioCatch afin d’identifier les utilisateurs à risque et frauduleux
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f9518466aacddee9e31d8bc15f3b89c1f214ab58
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737996"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Tutoriel : Configurer BioCatch avec Azure Active Directory B2C

Dans cet exemple de tutoriel, Découvrez comment intégrer l’authentification Azure Active Directory (AD) B2C avec [BioCatch](https://www.biocatch.com/) pour améliorer davantage votre posture de sécurité pour la gestion des identités et des accès client (CIAM). BioCatch analyse les comportements numériques cognitifs et physiques d’un utilisateur afin de générer des insights qui font la distinction entre les clients légitimes et les cybercriminels.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un compte [BioCatch](https://www.biocatch.com/contact-us).

## <a name="scenario-description"></a>Description du scénario

L’intégration BioCatch inclut les composants suivants :

- **Une application web ou un service web** : l’utilisateur accède pour la première fois à ce service web. Ce service web instancie un ID de session client unique qui est envoyé à BioCatch. L’ID de session client commence immédiatement à transmettre des caractéristiques de comportement utilisateur à BioCatch.

- **Une méthode** : envoie l’ID de session client unique à Azure AD B2C. Dans l’exemple fourni, JavaScript est utilisé pour entrer la valeur dans un champ HTML masqué.

- **Une interface utilisateur Azure AD B2C personnalisée** : masque un champ HTML pour l’entrée de l’ID de session client à partir de JavaScript, si vous utilisez la méthode ci-dessus

- **Stratégie personnalisée Azure Active Directory B2C**

  - Prend l’ID de session client personnalisé de l’interface utilisateur sous la forme d’une revendication. Cela est possible via un profil technique auto-déclaré

  - S’intègre à BioCatch via un fournisseur de revendications d’API REST et transmet l’ID de session client à la plateforme BioCatch

  - Plusieurs revendications personnalisées sont retournées à partir de BioCatch sur lesquelles la logique de stratégie agit ensuite

  - Un parcours utilisateur, qui évalue une revendication retournée, par exemple un risque de session, et exécute de manière conditionnelle une action, comme un appel à l’authentification multifacteur.

![Diagramme de l’architecture de BioCatch.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Étape  | Description |
|:---|:-----------------------|
|1a     | L’utilisateur navigue sur le service web. Le service web retourne ensuite des valeurs HTML, CSS ou JavaScript et configure le chargement du kit de développement logiciel (SDK) JavaScript BioCatch. Le JavaScript côté client configure/définit l’ID de session client pour le kit de développement logiciel (SDK) BioCatch. Le service web peut également préconfigurer l’ID de session client et l’envoyer au client.        |
|1b    |  Configurez le SDK JavaScript instancié pour la plateforme BioCatch. Commencez immédiatement à envoyer des caractéristiques de comportement utilisateur à la BioCatch depuis l’appareil client, en utilisant l’ID de session client de l’étape 1a.    |
|2     |  L’utilisateur tente de s’inscrire/se connecter et est redirigé vers Azure AD B2C.      |
|3a    | Une partie du parcours utilisateur est un fournisseur de réclamations auto-déclaré, qui prend l’ID de session client comme entrée. Ce champ est masqué à l’écran. Vous pouvez utiliser JavaScript pour entrer l’ID de session dans le champ. Cliquez sur le bouton *Suivant* pour continuer le processus d’inscription/de connexion.|
|3b     | L’ID de session client est envoyé à la plateforme BioCatch pour déterminer un score de risque. |
|3c     | BioCatch retourne des informations sur la session, comme le score de risque et une recommandation sur la marche à suivre : autoriser ou bloquer |
|3d    |Le parcours utilisateur a une étape de vérification conditionnelle, qui agit sur les revendications retournées|
| 4   | En fonction du résultat de la vérification conditionnelle, une action telle que l’*authentification multifacteur supérieure* est appelée|
|5    | À tout moment, lorsque l’utilisateur accède pour la première fois à la page du service web, le service web peut utiliser l’ID de session client pour interroger l’API BioCatch afin de déterminer le score de risque et les informations de session en temps réel. |

## <a name="onboard-with-biocatch"></a>Intégration avec BioCatch

Contactez [BioCatch](https://www.biocatch.com/contact-us) et créez un compte.

## <a name="configure-the-custom-ui"></a>Configurer l’interface utilisateur personnalisée

Il est recommandé de masquer le champ d’ID de session client. Utilisez CSS, JavaScript ou toute autre méthode pour masquer le champ. À des fins de test, vous pouvez l’afficher. Par exemple, JavaScript est utilisé pour masquer le champ d’entrée comme suit :

```JavaScript
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Configurer les stratégies Active Directory B2C Identity Experience Framework

1. Configurez la [Configuration de stratégie personnalisée](./tutorial-create-user-flows.md?pivots=b2c-custom-policy) initiale.

2. Créez un nouveau fichier, qui hérite du fichier d’extensions.

    ```XML
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Créez une référence à l’interface utilisateur personnalisée pour masquer la zone d’entrée, sous la ressource BuildingBlocks.

    ```XML
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Ajoutez les revendications suivantes sous la ressource BuildingBlocks.

    ```XML
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Configurez le fournisseur de revendications auto-déclaré pour le champ d’ID de session client.

    ```XML
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Configurez le fournisseur de revendications de l’API REST pour BioCatch. 

    ```XML
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch vous fournira l’URL, l’ID client et l’ID d’utilisateur unique (uuID) à configurer. La revendication SessionID client est transmise en tant que paramètre QueryString à BioCatch. Vous pouvez choisir le type d’activité, par exemple *MAKE_PAYMENT*.

7. Configurer le parcours utilisateur ; suivre l’exemple

   1. Obtenir le clientSessionID en tant que revendication

   1. Appeler l’API BioCatch pour accéder aux informations de session

   1. Si le *risque* de revendication retourné est égal à *faible*, ignorez l’étape pour l’authentification multifacteur, sinon forcez l’authentification multifacteur de l’utilisateur

    ```XML
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Configurer sur la configuration d’une partie de confiance (facultatif)

    Il est utile de transmettre les informations renvoyées par BioCatch à votre application sous forme de revendications dans le jeton, en particulier *risklevel* et *score*.

    ```XML
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

Procédez comme suit pour ajouter les fichiers de stratégie à Azure AD B2C

1. Connectez-vous au  [**portail Azure**](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.

2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement**  dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.

3. Choisissez  **Tous les services**  dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez Azure Active Directory B2C.

4. Accédez à  **Azure AD B2C** > **Identity Experience Framework**

3. Chargez tous les fichiers de stratégie sur votre locataire.

## <a name="test-the-solution"></a>Test de la solution

1. [Inscrire une application factice, qui redirige vers JWT.MS](./tutorial-register-applications.md?tabs=app-reg-ga)  

2. Sous l’**Identity Experience Framework**, sélectionnez la stratégie que vous avez créée

3. Dans la fenêtre de stratégie, sélectionnez l’application factice JWT.MS, puis **Exécuter maintenant**

4. Suivez le processus d’inscription et créez un compte. Le jeton renvoyé à JWT.MS doit avoir 2 revendications pour riskLevel et score. Suivez l’exemple.  

    ```JavaScript
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Ressources supplémentaires

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
