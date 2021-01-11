---
title: Configurer un flux utilisateur d’inscription et de connexion
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux d’inscription et de connexion dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618597"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Configurer un flux d’inscription et de connexion dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Flux d’inscription et de connexion

La stratégie d’inscription et de connexion permet aux utilisateurs d’effectuer les actions suivantes : 

* Inscription avec un compte local
* Connexion avec un compte local
* Inscription ou connexion avec un compte social
* Réinitialisation du mot de passe

![Flux de modification du profil](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Prérequis

Si ce n’est pas déjà fait, [inscrivez une application web dans Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Le flux d’utilisateur Inscription et connexion gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs de votre application sont dirigés vers le chemin approprié en fonction du contexte.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.

    ![Page Flux d'utilisateur du portail avec bouton Nouveau flux d'utilisateur en surbrillance](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**.

    ![Page Sélectionner un flux d'utilisateur avec Inscription et connexion en surbrillance](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)

    ![Page Créer un flux d'utilisateur du Portail Azure avec propriétés en surbrillance](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Pour **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.
1. Pour **Attributs utilisateur et revendications**, choisissez les revendications et les attributs à collecter et envoyer par l’utilisateur pendant l’inscription. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

    ![Page de sélection des attributs et revendications avec trois revendications sélectionnées](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis sélectionnez **S’inscrire maintenant**.

    ![Page Exécuter le flux d'utilisateur du portail avec bouton Exécuter le flux d'utilisateur en surbrillance](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. Entrez une adresse e-mail valide, cliquez sur **Envoyer un code de vérification**, entrez le code de vérification que vous recevez, puis sélectionnez **Vérifier le code**.
1. Entrez un nouveau mot de passe et confirmez-le.
1. Sélectionnez votre pays et votre région, entrez le nom que vous voulez afficher, entrez un code postal, puis cliquez sur **Créer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
1. Vous pouvez maintenant réexécuter le flux d’utilisateur et vous connecter avec le compte que vous avez créé. Le jeton retourné comprend les revendications que vous avez sélectionnées pour le pays/région, le nom et le code postal.

> [!NOTE]
> L’expérience « Exécuter le flux utilisateur » n’est actuellement pas compatible avec le type d’URL de réponse SPA utilisant le flux de code d’autorisation. Pour utiliser l’expérience « Exécuter le flux d’utilisateur » avec ces types d’applications, enregistrez une URL de réponse de type « Web » et activez le flux implicite, comme décrit [ici](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Créer une stratégie d’inscription et de connexion

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies, par exemple l’inscription et la connexion, la réinitialisation du mot de passe et la stratégie de modification de profil. Pour plus d’informations, consultez [Démarrage avec les stratégies personnalisées dans Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* Ajoutez une [connexion avec un fournisseur d’identité sociale](add-identity-provider.md).
* Configurez un [flux de réinitialisation de mot de passe](add-password-reset-policy.md).
