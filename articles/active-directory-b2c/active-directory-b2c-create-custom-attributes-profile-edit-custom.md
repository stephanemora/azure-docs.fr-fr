---
title: Ajouter vos propres attributs à des stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: Une procédure pas à pas sur l’utilisation des propriétés d’extension et des attributs personnalisés, ainsi que sur la manière de les inclure dans l’interface utilisateur.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449173"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C : création et utilisation d’attributs personnalisés dans une stratégie personnalisée de modification de profil

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans cet article, vous allez créer un attribut personnalisé dans votre répertoire Azure AD B2C et utiliser ce nouvel attribut comme une revendication personnalisée dans le parcours utilisateur Modification de profil.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans l’article [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Utilisation d’attributs personnalisés pour recueillir des informations sur vos consommateurs dans Azure Active Directory B2C à l’aide des stratégies personnalisées
Votre répertoire Azure Active Directory (Azure AD) B2C est fourni avec un ensemble intégré d’attributs : prénom, nom, ville, code postal, userPrincipalName, etc.  Vous serez souvent amené à créer vos propres attributs.  Par exemple : 
* Une application côté client a besoin de conserver un attribut tel que « LoyaltyNumber ».
* Un fournisseur d’identité a un identificateur d’utilisateur unique qui doit être enregistré, par exemple « uniqueUserGUID ».
* Un parcours utilisateur personnalisé doit conserver un état de l’utilisateur, par exemple « migrationStatus ».

Avec Azure AD B2C, vous pouvez étendre l’ensemble d’attributs stocké sur chaque compte utilisateur. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Les propriétés d’extension étendent le schéma des objets utilisateur dans le répertoire.  Les termes propriété d’extension, attribut personnalisé et revendication personnalisée font référence à la même chose dans le contexte de cet article, et le nom varie en fonction du contexte (application, objet, de stratégie).

Les propriétés d’extension ne peuvent être inscrites que sur un objet application, même si elles peuvent contenir des données pour un utilisateur. La propriété est attachée à l’application. L’accès en écriture pour enregistrer une propriété d’extension doit être accordé à l’objet application. 100 propriétés d’extension (de TOUS les types et de TOUTES les applications) peuvent être écrites dans n’importe objet unique. Les propriétés d’extension sont ajoutées au type de répertoire cible et deviennent immédiatement accessibles dans le client du répertoire Azure AD B2C.
Si l’application est supprimée, ces propriétés d’extension, ainsi que toutes les données qu’elles contiennent pour tous les utilisateurs, sont également supprimées. Si une propriété d’extension est supprimée par l’application, elle est supprimée de l’objet de répertoire cible, et les valeurs sont supprimées.

Les propriétés d’extension n’existent que dans le contexte d’une application inscrite dans le client. L’ID d’objet de cette application doit être inclus dans le TechnicalProfile qui l’utilise.

>[!NOTE]
>Le répertoire Azure AD B2C inclut généralement une application web nommée `b2c-extensions-app`.  Cette application est principalement utilisée par les stratégies B2C intégrées pour les revendications personnalisées créées via le portail Azure.  Il est recommandé, pour les utilisateurs expérimentés, d’utiliser cette application pour enregistrer des extensions pour les stratégies B2C personnalisées.  Les instructions de cette procédure figurent dans la section Étapes suivantes de cet article.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Création d’une nouvelle application pour stocker les propriétés d’extension

1. Ouvrez une session de navigation et accédez au [portail Azure](https://portal.azure.com), puis connectez-vous avec les informations d’identification administratives du répertoire B2C que vous souhaitez configurer.
2. Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**. Vous devrez peut-être sélectionner Plus de services> pour le trouver.
3. Sélectionnez **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.
4. Indiquez les entrées recommandées ci-après :
    * Spécifiez un nom pour l’application web : **WebApp-GraphAPI-DirectoryExtensions**
    * Type d’application : application web/API
    * URL d’ouverture de session : https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
5. Sélectionnez **Créer**.
6. Sélectionnez l’application web que vous venez de créer.
7. Sélectionnez **Paramètres** > **Autorisations requises**.
8. Sélectionnez l’API **Windows Azure Active Directory**.
9. Dans Autorisations d’application, cochez l’option **Lire et écrire les données de l’annuaire**, puis sélectionnez **Enregistrer**.
10. Sélectionnez **Accorder des autorisations**, puis confirmez en cliquant sur **Oui**.
11. Copiez dans le Presse-papiers et enregistrez les identificateurs suivants :
    * **ID de l’application**. Exemple : `103ee0e6-f92d-4183-b576-8c3739027780`
    * **ID objet**. Exemple : `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modification de votre stratégie personnalisée pour ajouter ApplicationObjectId

Lorsque vous effectué les étapes décrites dans l’article [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md), vous avez téléchargé et modifié les [fichiers](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nommés *TrustFrameworkBase.xml*,  *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* et *PasswordReset.xml*. Dans les étapes suivantes, vous continuez à modifier ces fichiers.

1. Ouvrez le fichier *TrustFrameworkBase.xml*, puis ajoutez la section `Metadata` comme indiqué dans l’exemple suivant. Insérer l’ID d’objet que vous avez enregistré précédemment pour la valeur `ApplicationObjectId` et l’ID d’application que vous avez enregistré pour la valeur `ClientId` : 

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

>[!NOTE]
>Lorsque le TechnicalProfile écrit pour la première fois dans la propriété d’extension qui vient d’être créée, vous pouvez rencontrer une erreur unique. La propriété d’extension est créée lors de la première utilisation.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Utilisation d’une nouvelle propriété d’extension ou d’un nouvel attribut personnalisé dans un parcours utilisateur

1. Ouvrez le fichier *ProfileEdit.xml*.
2. Ajoutez une revendication personnalisée `loyaltyId`.  La revendication personnalisée de l’élément `<RelyingParty>` est incluse dans le jeton de l’application.
    
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

3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis ajoutez l’élément `<ClaimsSchema>` et ses éléments enfants à l’élément `BuildingBlocks` :

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

4. Ajoutez la même définition `ClaimType` à *TrustFrameworkBase.xml*. L’ajout d’une définition `ClaimType` dans les fichiers de base et d’extensions n’est normalement pas nécessaire. Toutefois, étant donné que, dans les étapes suivantes, vous ajouterez `extension_loyaltyId` aux éléments TechnicalProfiles du fichier de base, sans cette définition, le programme de validation de stratégie rejettera le chargement du fichier de base. Il peut être utile de suivre l’exécution du parcours utilisateur nommé « ProfileEdit » dans le fichier *TrustFrameworkBase.xml*.  Recherchez le parcours utilisateur du même nom dans votre éditeur et vérifiez que l’étape d’orchestration 5 appelle TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Recherchez et examinez ce TechnicalProfile pour vous familiariser avec le flux.

5. Ouvrez le fichier *TrustFrameworkBase.xml*, puis ajoutez `loyaltyId` en tant que revendication d’entrée et de sortie dans l’élément « SelfAsserted-ProfileUpdate » du fichier TechnicalProfile :

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

6. Dans le fichier *TrustFrameworkBase.xml*, ajoutez la revendication `loyaltyId` à l’élément TechnicalProfile « AAD-UserWriteProfileUsingObjectId » pour conserver la valeur de la revendication dans la propriété d’extension, pour l’utilisateur actuellement présent dans le répertoire :

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

7. Dans le fichier *TrustFrameworkBase.xml*, ajoutez la revendication `loyaltyId` à l’élément TechnicalProfile « AAD UserReadUsingObjectId » pour lire la valeur de l’attribut d’extension chaque fois qu’un utilisateur se connecte. Jusqu'à présent les éléments TechnicalProfiles ont été modifiés dans le flux des comptes locaux uniquement.  Si vous souhaitez placer le nouvel attribut dans le flux d’un compte fédéré/social, vous devez modifier un autre ensemble d’éléments TechnicalProfiles. Reportez-vous aux étapes suivantes.

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

1. Ouvrez le **panneau Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité > Stratégies personnalisées**.
1. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

Le jeton d’ID renvoyé à votre application inclura la nouvelle propriété d’extension sous la forme d’une revendication personnalisée, précédée de « extension_loyaltyId ». Consultez les exemples.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Ajoutez la nouvelle revendication aux flux des connexions aux comptes sociaux en modifiant les éléments TechnicalProfiles répertoriés ci-dessous. Ces deux éléments TechnicalProfiles sont utilisés par les connexions aux comptes sociaux/fédérés pour écrire et lire les données utilisateur à l’aide d’alternativeSecurityId en tant que localisateur de l’objet utilisateur.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Utilisation des mêmes attributs d’extension pour les stratégies intégrées et les stratégies personnalisées.
Lorsque vous ajoutez des attributs d’extension (ou attributs personnalisés) via le portail, ils sont inscrits à l’aide de **b2c-extensions-app, qui se trouve dans chaque locataire B2C.  Pour utiliser ces attributs d’extension dans votre stratégie personnalisée :
1. Sur portal.azure.com, dans votre locataire B2C, accédez à **Azure Active Directory**, puis sélectionnez **Inscriptions des applications**
2. Recherchez **b2c-extensions-app**, puis sélectionnez-la.
3. Sous Éléments principaux, notez **l’ID de l’application** et **l’ID de l’objet**.
4. Ajoutez-les aux métadonnées de votre profil technique AAD-Common de la façon suivante :

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

Pour maintenir une cohérence avec le portail, créez ces attributs dans l’interface utilisateur du portail *avant* de les utiliser dans vos stratégies personnalisées.  Lorsque vous créez un attribut « ActivationStatus » dans le portail, vous devez le référencer de la façon suivante :

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Informations de référence

* Un **profil technique (PT)** est un type d’élément qui peut être considéré comme une *fonction* qui définit le nom d’un point de terminaison, ses métadonnées et son protocole, et détaille l’échange de revendications que l’IEF doit effectuer.  Lorsque cette *fonction* est appelée au cours d’une étape d’orchestration ou à partir d’un autre élément TechnicalProfile, InputClaims et OutputClaims sont fournis comme paramètres par l’appelant.


* Pour plus d’informations sur les propriétés d’extension, consultez l’article [Extensions de schéma d’annuaire | Concepts de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

>[!NOTE]
>Les attributs d’extension dans l’API Graph sont nommés à l’aide de la convention `extension_ApplicationObjectID_attributename`. Les stratégies personnalisées désignent les attributs d’extensions par extension_attributename, omettant ainsi l’élément ApplicationObjectId dans le fichier XML
