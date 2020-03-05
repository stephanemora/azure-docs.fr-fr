---
title: Ajouter vos propres attributs à des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Procédure pas à pas sur l’utilisation des propriétés d’extension et des attributs personnalisés, ainsi que sur la manière de les inclure dans l’interface utilisateur.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c145b0efa2f1c06710e1d41f606f918a7439820a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189495"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C : Utiliser des attributs personnalisés dans une stratégie personnalisée de modification de profil

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans cet article, vous allez créer un attribut personnalisé dans votre annuaire Azure Active Directory B2C (Azure AD B2C). Vous allez utiliser ce nouvel attribut comme revendication personnalisée dans le parcours de l’utilisateur pour modifier des profils.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes de l’article [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Utilisez les attributs personnalisés pour collecter des informations sur vos utilisateurs dans Azure AD B2C à l’aide de stratégies personnalisées.
Votre annuaire Azure AD B2C comprend un ensemble intégré d’attributs. Voici quelques exemples d’attributs : **Given Name**, **Surname**, **City**, **Postal Code** et **userPrincipalName**. Vous serez souvent amené à créer vos propres attributs, tels que ceux-ci :
* Une application côté client a besoin de conserver un attribut tel que **LoyaltyNumber**.
* Un fournisseur d’identité a un identificateur d’utilisateur unique, tel que **uniqueUserGUID**, qui doit être enregistré.
* Un parcours utilisateur personnalisé doit conserver l’état de l’utilisateur, par exemple **migrationStatus**.

Azure AD B2C étend l’ensemble d’attributs stocké dans chaque compte utilisateur. Vous pouvez également lire et écrire ces attributs à l’aide de l’[API Microsoft Graph](manage-user-accounts-graph-api.md).

Les propriétés d’extension étendent le schéma des objets utilisateur dans le répertoire. Dans cet article, les termes *propriété d’extension*, *attribut personnalisé* et *revendication personnalisée* font référence à la même chose. Le nom varie en fonction du contexte (application, objet, stratégie).

Les propriétés d’extension ne peuvent être inscrites que pour un objet application, même si elles peuvent contenir les données d’un utilisateur. La propriété est attachée à l’application. L’accès en écriture pour inscrire une propriété d’extension doit être accordé à l’objet application. Un même objet peut avoir 100 propriétés d’extension, pour tous les types et toutes les applications. Les propriétés d’extension sont ajoutées au type de l’annuaire cible et deviennent immédiatement accessibles dans le locataire de l’annuaire Azure AD B2C.
Si l’application est supprimée, ces propriétés d’extension, ainsi que toutes les données qu’elles contiennent pour tous les utilisateurs, sont également supprimées. Si une propriété d’extension est supprimée par l’application, elle est supprimée des objets de l’annuaire cible, et les valeurs sont supprimées.

Les propriétés d’extension n’existent que dans le contexte d’une application inscrite dans le locataire. L’ID d’objet de cette application doit être inclus dans le **TechnicalProfile** qui l’utilise.

>[!NOTE]
>L’annuaire Azure AD B2C inclut généralement une application web nommée `b2c-extensions-app`. Cette application est principalement utilisée par les stratégies B2C intégrées pour les revendications personnalisées créées via le portail Azure. Il est recommandé que seuls les utilisateurs expérimentés utilisent cette application dans le but d’inscrire des extensions pour les stratégies B2C personnalisées.
Les instructions de cette procédure figurent dans la section **Étapes suivantes** de cet article.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Créer une application pour stocker les propriétés d’extension

1. Ouvrez une session de navigation et accédez au [portail Azure](https://portal.azure.com). Connectez-vous à l’aide des informations d’identification administrateur de l’annuaire B2C que vous souhaitez configurer.
2. Dans le menu de navigation gauche, sélectionnez **Azure Active Directory**. Vous devrez peut-être sélectionner **Plus de services** pour le trouver.
3. Sélectionnez **Inscriptions d’applications**. Sélectionnez **Nouvelle inscription d’application**.
4. Fournissez les informations suivantes :
    * Nom de l’application web : **WebApp-GraphAPI-DirectoryExtensions**.
    * Type d’application : **Application/API web**.
    * L’URL de connexion : **https://{nom_du_locataire}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Sélectionnez **Create** (Créer).
6. Sélectionnez l’application web que vous venez de créer.
7. Sélectionnez **Paramètres** > **Autorisations requises**.
8. Sélectionnez l’API **Microsoft Azure Active Directory**.
9. Cochez les autorisations d’application : **Accéder en lecture et en écriture aux données de l’annuaire**. Ensuite, sélectionnez **Enregistrer**.
10. Sélectionnez **Accorder des autorisations**, puis confirmez en cliquant sur **Oui**.
11. Copiez les identificateurs suivants dans le Presse-papiers, puis enregistrez-les :
    * **ID de l’application**. Exemple : `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID objet**. Exemple : `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modifier votre stratégie personnalisée pour ajouter **ApplicationObjectId**

Lorsque vous avez suivi les étapes d’[Azure Active Directory B2C : Bien démarrer avec les stratégies personnalisées](custom-policy-get-started.md), vous avez téléchargé et modifié les [fichiers exemples](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nommés **TrustFrameworkBase.xml**,  **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml** et **PasswordReset.xml**. Dans cette étape, vous allez apporter davantage de modifications à ces fichiers.

* Ouvrez le fichier **TrustFrameworkBase.xml**, puis ajoutez la section `Metadata` comme indiqué dans l’exemple suivant. Insérez l’ID d’objet que vous avez enregistré précédemment pour la valeur `ApplicationObjectId` et l’ID d’application que vous avez enregistré pour la valeur `ClientId` :

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Lorsque le **TechnicalProfile** écrit pour la première fois dans la propriété d’extension qui vient d’être créée, vous pouvez rencontrer une erreur. La propriété d’extension est créée lors de sa première utilisation.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Utiliser une nouvelle propriété d’extension ou un nouvel attribut personnalisé dans un parcours utilisateur

1. Ouvrez le fichier **ProfileEdit.xml**.
2. Ajoutez une revendication personnalisée `loyaltyId`. La revendication personnalisée de l’élément `<RelyingParty>` est incluse dans le jeton de l’application.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Ouvrez le fichier **TrustFrameworkExtensions.xml**, puis ajoutez l’élément `<ClaimsSchema>` et ses éléments enfants à l’élément `BuildingBlocks` :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Ajoutez la même définition `ClaimType` à **TrustFrameworkBase.xml**. Il n’est pas nécessaire d’ajouter une définition `ClaimType` à la fois dans les fichiers de base et dans les fichiers d’extension. Toutefois, les étapes suivantes ajoutent `extension_loyaltyId` aux **TechnicalProfiles** dans le fichier de base. Par conséquent, le validateur de stratégie rejette le chargement du fichier de base si celui-ci ne contient pas cet ID. Il peut être utile de suivre l’exécution du parcours utilisateur nommé **ProfileEdit** dans le fichier **TrustFrameworkBase.xml**. Recherchez le parcours utilisateur du même nom dans votre éditeur. Vérifiez que l’étape d’orchestration 5 appelle **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**. Recherchez et examinez ce **TechnicalProfile** pour vous familiariser avec le flux.

5. Ouvrez le fichier **TrustFrameworkBase.xml**, puis ajoutez `loyaltyId` en tant que revendication d’entrée et de sortie dans l’élément **TechnicalProfile SelfAsserted-ProfileUpdate** :

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. Dans le fichier **TrustFrameworkBase.xml**, ajoutez la revendication `loyaltyId` à **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Cet ajout conserve la valeur de la revendication dans la propriété d’extension, pour l’utilisateur actuel de l’annuaire :

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. Dans le fichier **TrustFrameworkBase.xml**, ajoutez la revendication `loyaltyId` à **TechnicalProfile AAD-UserReadUsingObjectId** pour lire la valeur de l’attribut d’extension chaque fois qu’un utilisateur se connecte. Jusqu’à présent, les **TechnicalProfiles** ont été modifiés uniquement dans le flux des comptes locaux. Si vous souhaitez placer le nouvel attribut dans le flux d’un compte fédéré ou social, vous devez modifier un autre ensemble de **TechnicalProfiles**. Consultez la section **Étapes suivantes**.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Ouvrez le panneau Azure AD B2C, puis accédez à **Infrastructure d’expérience d’identité** > **Stratégies personnalisées**.
1. Sélectionnez la stratégie personnalisée que vous avez chargée. Sélectionnez **Exécuter maintenant**.
1. Inscrivez-vous à l’aide d’une adresse e-mail.

Le jeton d’ID renvoyé à votre application inclut la nouvelle propriété d’extension sous la forme d’une revendication personnalisée, précédée de **extension_loyaltyId**. Voir l’exemple suivant :

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Étapes suivantes

1. Ajoutez la nouvelle revendication aux flux des connexions aux comptes sociaux en modifiant les **TechnicalProfiles** suivants. Les comptes sociaux et les comptes fédérés utilisent ces deux **TechnicalProfiles** pour se connecter. Ils écrivent et lisent des données utilisateur en se servant de **alternativeSecurityId** comme de localisateur d’objet utilisateur.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Utilisez les mêmes attributs d’extension pour les stratégies intégrées et les stratégies personnalisées. Lorsque vous ajoutez des attributs d’extension ou des attributs personnalisés via le portail, ils sont inscrits à l’aide de **b2c-extensions-app**, qui se trouve dans chaque locataire B2C. Pour utiliser ces attributs d’extension dans votre stratégie personnalisée, effectuez les étapes suivantes :

   a. Sur portal.azure.com, dans votre locataire B2C, accédez à **Azure Active Directory**, puis sélectionnez **Inscriptions des applications**.
   b. Recherchez **b2c-extensions-app**, puis sélectionnez-la.
   c. Sous **Éléments principaux**, entrez **l’ID de l’application** et **l’ID de l’objet**.
   d. Ajoutez-les aux métadonnées du TechnicalProfile **AAD-Common** :

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Maintenez une cohérence avec l’expérience du portail. Créez ces attributs dans l’interface utilisateur du portail avant de les utiliser dans vos stratégies personnalisées. Lorsque vous créez un attribut **ActivationStatus** dans le portail, vous devez le référencer de la façon suivante :

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Informations de référence

Pour plus d’informations sur les propriétés d’extension, voir l’article [Ajouter des données personnalisées à des ressources à l’aide d’extensions](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * Un **TechnicalProfile** est un type d’élément, ou une fonction, qui définit le nom, les métadonnées et le protocole d’un point de terminaison. Le **TechnicalProfile** détaille l’échange de revendications effectué par l’infrastructure d’expérience d’identité. Lorsque cette fonction est appelée au cours d’une étape d’orchestration ou à partir d’un autre **TechnicalProfile**, **InputClaims** et **OutputClaims** sont fournis comme paramètres par l’appelant.
> * Dans l’API Graph, les attributs d’extension sont nommés à l’aide de la convention `extension_ApplicationObjectID_attributename`.
> * Les stratégies personnalisées référencent les attributs d’extension de la manière suivante : **extension_nomAttribut**. Cette référence omet **ApplicationObjectId** dans le code XML.
> * Vous devez spécifier l’ID d’attribut, où qu’il soit référencé, au format suivant : **extension_attributename**.
