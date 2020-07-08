---
title: Configurer la complexité du mot de passe à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Guide pratique pour configurer les exigences de complexité du mot de passe à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389001"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la complexité du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans Azure Active Directory B2C (Azure AD B2C), vous pouvez configurer les critères de complexité des mots de passe qui sont fournis par un utilisateur lors de la création d’un compte. Par défaut, Azure AD B2C utilise des mots de passe **forts**. Cet article vous montre comment configurer la complexité du mot de passe dans des [stratégies personnalisées](custom-policy-overview.md). Il est également possible de configurer la complexité du mot de passe dans les [flux d’utilisateurs](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md). Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux.


## <a name="add-the-elements"></a>Ajouter les éléments

Pour configurer la complexité du mot de passe, remplacez les [types de revendication](claimsschema.md) `newPassword` et `reenterPassword` par une référence aux [validations de prédicat](predicates.md#predicatevalidations). L’élément PredicateValidations regroupe un ensemble de prédicats pour former une validation d’entrée utilisateur qui peut être appliquée à un type de revendication. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez les revendications `newPassword` et `reenterPassword` à l’élément **ClaimsSchema**.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicates](predicates.md) définit une validation de base pour vérifier la valeur d’un type de revendication et retourne true ou false. La validation est effectuée en utilisant un élément de méthode spécifié, et un ensemble de paramètres appropriés à la méthode. Ajoutez les prédicats suivants à l’élément **BuildingBlocks**, juste après la fermeture de l’élément `</ClaimsSchema>` :

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Ajoutez les validations de prédicat suivantes à l’élément **BuildingBlocks**, juste après la fermeture de l’élément `</Predicates>` :

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Les profils techniques suivants sont des [profils techniques Active Directory](active-directory-technical-profile.md) qui lisent et écrivent des données dans Azure Active Directory. Remplacez ces profils techniques dans le fichier d’extension. Utilisez `PersistedClaims` pour désactiver la stratégie de mot de passe fort. Recherchez l’élément **ClaimsProviders**.  Ajoutez les fournisseurs de revendication suivants de cette façon :

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Enregistrez le fichier de stratégie.

## <a name="test-your-policy"></a>Tester votre stratégie

### <a name="upload-the-files"></a>Téléchargement des fichiers

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Dans la page Stratégies personnalisées, cliquez sur **Charger une stratégie**.
6. Sélectionnez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
7. Cliquez sur **Télécharger**.

### <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie d’inscription ou de connexion. Par exemple, *B2C_1A_signup_signin*.
2. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton, l’**URL de réponse** doit indiquer `https://jwt.ms`.
3. Cliquez sur **Exécuter maintenant**.
4. Sélectionnez **Inscrivez-vous maintenant**, entrez une adresse e-mail et entrez un nouveau mot de passe. Des conseils sur les restrictions de mot de passe s’affichent. Finissez d’entrer les informations utilisateur, puis cliquez sur **Créer**. Vous devez voir le contenu du jeton qui a été retourné.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-password-change.md).
- Apprenez-en davantage sur les éléments [Predicates](predicates.md) et [PredicateValidations](predicates.md#predicatevalidations) dans la référence IEF.
