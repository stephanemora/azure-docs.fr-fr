---
title: Configurer la complexité du mot de passe à l’aide de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Guide pratique pour configurer les exigences de complexité du mot de passe à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8718a04f9d63897b2d2472dd0cdffb196c41435
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949787"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la complexité du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans Azure Active Directory B2C (Azure AD B2C), vous pouvez configurer les critères de complexité des mots de passe qui sont fournis par un utilisateur lors de la création d’un compte. Par défaut, Azure AD B2C utilise des mots de passe **forts**. Cet article vous montre comment configurer la complexité du mot de passe dans des [stratégies personnalisées](active-directory-b2c-overview-custom.md). Il est également possible de configurer la complexité du mot de passe dans les [flux d’utilisateurs](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes dans [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Ajouter les éléments

1. Copiez le fichier *SignUpOrSignIn.xml* que vous avez téléchargé avec le pack de démarrage et nommez-le *SingUpOrSignInPasswordComplexity.xml*.
2. Ouvrez le fichier *SingUpOrSignInPasswordComplexity.xml*, puis remplacez **PolicyId** et **PublicPolicyUri** par un nouveau nom de stratégie. Par exemple, *B2C_1A_signup_signin_password_complexity*.
3. Ajoutez les éléments **ClaimType** suivants avec les identificateurs `newPassword` et `reenterPassword` :

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. Les éléments [Predicates](predicates.md) ont les types de méthode `IsLengthRange` ou `MatchesRegex`. Le type `MatchesRegex` est utilisé pour correspondre à une expression régulière. Le type `IsLengthRange` prend une longueur de chaîne minimale et maximale. Ajoutez un élément **Predicates** à l’élément **BuildingBlocks** s’il n’existe pas avec les éléments **Predicate** suivants :

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Chaque élément **InputValidation** est construit à l’aide des éléments **Predicate** définis. Cet élément vous permet d’effectuer des agrégations booléennes comme `and` et `or`. Ajoutez un élément **InputValidations** à l’élément **BuildingBlocks** s’il n’existe pas avec l’élément **InputValidation** suivant :

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Assurez-vous que le profil technique **PolicyProfile** contient les éléments suivants :

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Enregistrez le fichier de stratégie.

## <a name="test-your-policy"></a>Tester votre stratégie

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

### <a name="upload-the-files"></a>Téléchargement des fichiers

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Dans la page Stratégies personnalisées, cliquez sur **Charger une stratégie**.
6. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *SingUpOrSignInPasswordComplexity.xml*.
7. Cliquez sur **Télécharger**.

### <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie que vous avez changée. Par exemple, *B2C_1A_signup_signin_password_complexity*.
2. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton, l’**URL de réponse** doit indiquer `https://jwt.ms`.
3. Cliquez sur **Exécuter maintenant**.
4. Sélectionnez **Inscrivez-vous maintenant**, entrez une adresse e-mail et entrez un nouveau mot de passe. Des conseils sur les restrictions de mot de passe s’affichent. Finissez d’entrer les informations utilisateur, puis cliquez sur **Créer**. Vous devez voir le contenu du jeton qui a été retourné.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


