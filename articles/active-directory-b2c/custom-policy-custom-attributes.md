---
title: Ajouter vos propres attributs à des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Procédure pas à pas sur l’utilisation des propriétés d’extension et des attributs personnalisés, ainsi que sur la manière de les inclure dans l’interface utilisateur.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389324"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C : Activer des attributs personnalisés dans une stratégie de profil personnalisée

Dans l’article [Ajouter des revendications et personnaliser une entrée d’utilisateur à l’aide de stratégies personnalisées](custom-policy-configure-user-input.md), vous apprenez à utiliser des [attributs de profil utilisateur](user-profile-attributes.md) intégrés. Dans cet article, vous allez activer un attribut personnalisé dans votre annuaire Azure Active Directory B2C (Azure AD B2C). Plus tard, vous pourrez utiliser le nouvel attribut simultanément en tant que revendication personnalisée dans les [flux d’utilisateurs](user-flow-overview.md) ou les [stratégies personnalisées](custom-policy-get-started.md) .

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Prérequis

Suivez les étapes de l’article [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Utiliser des attributs personnalisés pour recueillir des informations sur vos clients 

Votre annuaire Azure AD B2C comprend un [ensemble intégré d’attributs](user-profile-attributes.md). Toutefois, vous devez souvent créer vos propres attributs pour gérer votre scénario spécifique, par exemple dans les cas suivants :

* Une application côté client a besoin de conserver un attribut tel que **LoyaltyId**.
* Un fournisseur d’identité a un identificateur d’utilisateur unique, **uniqueUserGUID**, qui doit être enregistré.
* Un parcours utilisateur personnalisé doit enregistrer l’état de l’utilisateur, **migrationStatus**, pour que d’autres logiques fonctionnent dessus.

Azure AD B2C vous permet d’étendre l’ensemble d’attributs stocké sur chaque compte d’utilisateur. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Application d’extensions Azure AD B2C

Les attributs d’extension ne peuvent être inscrits que pour un objet application, même s’ils peuvent contenir les données d’un utilisateur. L’attribut d’extension est attaché à l’application appelée b2c-extensions-app. Ne modifiez pas cette application, car elle est utilisée par Azure AD B2C pour le stockage des données utilisateurs. Vous trouverez cette application sous Azure AD B2C, inscriptions d’applications.

Dans cet article, les termes *propriété d’extension*, *attribut personnalisé* et *revendication personnalisée* font référence à la même chose. Le nom varie en fonction du contexte (application, objet, stratégie).

## <a name="get-the-application-properties"></a>Obtenir les propriétés de l’application

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez **Toutes les applications**.
1. Sélectionner l’application `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copiez les identificateurs suivants dans le Presse-papiers, puis enregistrez-les :
    * **ID de l’application**. Exemple : `11111111-1111-1111-1111-111111111111`.
    * **ID objet**. Exemple : `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modifier votre stratégie personnalisée

Pour activer des attributs personnalisés dans votre stratégie, fournissez l’**ID de l’application** et l’**ID d’objet** de l’application dans les métadonnées du profil technique AAD-Common. Le profil technique *AAD-Common* se trouve dans le profil technique de base [Azure Active Directory](active-directory-technical-profile.md) et prend en charge la gestion des utilisateurs Azure AD. D’autres profils techniques Azure AD incluent AAD-Common pour tirer parti de cette configuration. Remplacez le profil technique AAD-Common dans le fichier d’extension.

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément ClaimsProviders. Ajoutez un nouveau ClaimsProvider à l’élément ClaimsProviders.
1. Remplacez `ApplicationObjectId` par l’ID d’objet que vous avez enregistré précédemment. Remplacez ensuite `ClientId` par l’ID d’application que vous avez enregistré précédemment dans l’extrait de code ci-dessous.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Télécharger votre stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les fichiers de stratégie TrustFrameworkExtensions.xml que vous avez modifiés.

> [!NOTE]
> La première fois que le profil technique Azure AD enregistre la revendication dans l’annuaire, il vérifie si l’attribut personnalisé existe. Si ce n’est pas le cas, il crée l’attribut personnalisé.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Créer un attribut personnalisé via le portail Azure

Les mêmes attributs d’extension sont partagés entre les stratégies intégrées et les stratégies personnalisées. Lorsque vous ajoutez des attributs personnalisés via le portail, ils sont inscrits à l’aide de **b2c-extensions-app**, qui se trouve dans chaque locataire B2C.

Vous pouvez créer ces attributs dans l’interface utilisateur du portail avant ou après leur utilisation dans vos stratégies personnalisées. Suivez les instructions pour [définir des attributs personnalisés dans Azure Active Directory B2C](user-flow-custom-attributes.md). Lorsque vous créez un attribut **loyaltyId** dans le portail, vous devez y faire référence de la façon suivante :

|Nom     |Utilisé dans |
|---------|---------|
|`extension_loyaltyId`  | Stratégie personnalisée|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API Microsoft Graph](manage-user-accounts-graph-api.md)|

L’exemple suivant illustre l’utilisation d’attributs personnalisés dans une définition de revendication de stratégie personnalisée Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

L’exemple suivant illustre l’utilisation d’un attribut personnalisé au sein d’une stratégie personnalisée Azure AD B2C dans un profil technique, une entrée, une sortie et des revendications persistantes.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Utiliser un attribut personnalisé dans une stratégie

Suivez les instructions pour [ajouter des revendications et personnaliser l’entrée utilisateur avec des stratégies personnalisées](custom-policy-configure-user-input.md). Cet exemple utilise une revendication intégrée « City ». Pour utiliser un attribut personnalisé, remplacez « City » par vos propres attributs personnalisés.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Attributs de profil utilisateur Azure AD B2C](user-profile-attributes.md)
- [Définition des attributs d’extension](user-profile-attributes.md#extension-attributes)
- [Gérer les comptes d’utilisateur Azure AD B2C avec Microsoft Graph](manage-user-accounts-graph-api.md)
