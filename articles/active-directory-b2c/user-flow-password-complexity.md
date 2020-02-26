---
title: Configurer les critères de complexité des mots de passe
titleSuffix: Azure AD B2C
description: Guide pratique pour configurer les exigences de complexité des mots de passe fournis par les consommateurs dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6e629f3c83d847cf20eccbe7a3fb0d3f444dac62
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430327"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurer les exigences de complexité pour les mots de passe dans Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) prend en charge la modification des exigences de complexité des mots de passe fournis par un utilisateur final à la création d’un compte. Par défaut, Azure AD B2C utilise des mots de passe `Strong`. Azure AD B2C prend également en charge les options de configuration permettant de contrôler la complexité des mots de passe utilisables par les clients.

## <a name="password-rule-enforcement"></a>Application des règles de mot de passe

Lors de l’inscription ou de la réinitialisation de mot de passe, un utilisateur final doit fournir un mot de passe qui respecte les règles de complexité. Les règles de complexité du mot de passe s’appliquent flux d’utilisateur par flux d’utilisateur. Il est possible qu’un flux d’utilisateur demande un code confidentiel à quatre chiffres et un autre une chaîne de huit caractères à l’inscription. Par exemple, vous pouvez utiliser un flux d’utilisateur avec différents niveaux de complexité du mot de passe pour les adultes et pour les enfants.

La complexité du mot de passe ne s’applique jamais lors de la connexion. Les utilisateurs ne sont jamais invités à modifier leur mot de passe parce qu’il ne répond pas à l’exigence de complexité actuelle.

Vous pouvez configurer la complexité des mot de passe dans les types de flux d’utilisateurs suivants :

- Flux d’utilisateur d’inscription ou de connexion
- Flux d’utilisateur de réinitialisation du mot de passe

Si vous utilisez des stratégies personnalisées, vous pouvez ([configurer la complexité des mots de passe dans une stratégie personnalisée](custom-policy-password-complexity.md)).

## <a name="configure-password-complexity"></a>Configurer la complexité du mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
3. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux utilisateur (stratégies)** .
2. Sélectionnez un flux d’utilisateur, puis cliquez sur **Propriétés**.
3. Sous **Complexité du mot de passe**, changez la complexité du mot de passe de ce flux d’utilisateur : **Simple**, **Fort** ou **Personnalisé**.

### <a name="comparison-chart"></a>Tableau comparatif

| Complexité | Description |
| --- | --- |
| Simple | Un mot de passe entre 8 et 64 caractères. |
| Remarque | Un mot de passe entre 8 et 64 caractères. Trois de ces quatre types d’éléments : minuscules, majuscules, chiffres ou symboles. |
| Custom | C’est l’option qui fournit le contrôle le plus poussé sur les règles de complexité du mot de passe.  Elle permet de configurer une longueur personnalisée,  et d’accepter les mots de passe composés exclusivement de chiffres (codes confidentiels). |

## <a name="custom-options"></a>Options personnalisées

### <a name="character-set"></a>Jeu de caractères

Permet d’accepter des chiffres uniquement (code confidentiel) ou la totalité du jeu de caractères.

- **Numbers only** (Chiffres uniquement) autorise la saisie de mots de passe composés exclusivement de chiffres (0-9).
- **All** (Tout) accepte toutes les lettres, tous les chiffres et tous les symboles.

### <a name="length"></a>Longueur

Permet de contrôler les critères de longueur du mot de passe.

- **Minimum Length** (Longueur minimale) doit avoir au moins la valeur 4.
- **Maximum Length** (Longueur maximale) doit avoir une valeur supérieure ou égale à la longueur minimale, au maximum 64 caractères.

### <a name="character-classes"></a>Classes de caractères

Permet de contrôler les différents types de caractères utilisés dans le mot de passe.

- **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (2 sur 4 : minuscule, majuscule, chiffre (0-9), symbole) garantit que le mot de passe contient au moins trois types de caractères, par exemple, un nombre et une minuscule.
- **3 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (3 sur 4 : Minuscule, Majuscule, Chiffre (0-9), Symbole) garantit que le mot de passe contient au moins trois types de caractères. par exemple, un chiffre, une minuscule et une majuscule.
- **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (4 sur 4 : minuscule, majuscule, chiffre (0-9), symbole) garantit que le mot de passe contient les quatre types de caractères.

    > [!NOTE]
    > La condition **4 sur 4** peut contrarier l’utilisateur final. Des études ont montré qu’elle n’améliore pas l’entropie du mot de passe. Consultez les [Recommandations du NIST en matière de mots de passe](https://pages.nist.gov/800-63-3/sp800-63b.html#appA).
