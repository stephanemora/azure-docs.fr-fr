---
title: Tutoriel – Créer des flux utilisateur – Azure Active Directory B2C
description: Découvrez comment créer des flux utilisateur sur le portail Azure pour permettre l’inscription, la connexion et la modification de profil utilisateur pour vos applications dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183565"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutoriel : Créer des flux d’utilisateur dans Azure Active Directory B2C

Vos applications comportent peut-être des [flux utilisateur](user-flow-overview.md) qui permettent aux utilisateurs de s’inscrire, de se connecter ou de gérer leur profil. Vous pouvez créer plusieurs flux d’utilisateurs de types différents dans votre locataire Azure Active Directory B2C (Azure AD B2C) et les utiliser dans vos applications selon les besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

Ce tutoriel vous montre comment créer des flux d’utilisateur recommandés à l’aide du portail Azure. Si vous recherchez des informations sur la façon de configurer un flux ROPC (informations d’identification de mot de passe du propriétaire de la ressource) dans votre application, consultez [Configurer le flux des informations d’identification par mot de passe du propriétaire de ressource dans Azure AD B2C](configure-ropc.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

[Inscrivez vos applications](tutorial-register-applications.md) qui font partie des flux d’utilisateur à créer.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Le flux d’utilisateur Inscription et connexion gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs de votre application sont dirigés vers le chemin approprié en fonction du contexte.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez le répertoire qui contient votre locataire Azure AD B2C.

    ![Volet client B2C, répertoire et abonnement, Portail Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux utilisateur (stratégies)** , puis **Nouveau flux d’utilisateur**.

    ![Page Flux d'utilisateur du portail avec bouton Nouveau flux d'utilisateur en surbrillance](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Sous l’onglet **Recommandé**, sélectionnez le flux utilisateur **Inscription et connexion**.

    ![Page Sélectionner un flux d'utilisateur avec Inscription et connexion en surbrillance](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Pour **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.

    ![Page Créer un flux d'utilisateur du Portail Azure avec propriétés en surbrillance](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Pour **Attributs utilisateur et revendications**, choisissez les revendications et les attributs à collecter et envoyer par l’utilisateur pendant l’inscription. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

    ![Page de sélection des attributs et revendications avec trois revendications sélectionnées](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis sélectionnez **S’inscrire maintenant**.

    ![Page Exécuter le flux d'utilisateur du portail avec bouton Exécuter le flux d'utilisateur en surbrillance](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Entrez une adresse e-mail valide, cliquez sur **Envoyer un code de vérification**, entrez le code de vérification que vous recevez, puis sélectionnez **Vérifier le code**.
1. Entrez un nouveau mot de passe et confirmez-le.
1. Sélectionnez votre pays et votre région, entrez le nom que vous voulez afficher, entrez un code postal, puis cliquez sur **Créer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
1. Vous pouvez maintenant réexécuter le flux d’utilisateur et vous connecter avec le compte que vous avez créé. Le jeton retourné comprend les revendications que vous avez sélectionnées pour le pays/région, le nom et le code postal.

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Si vous voulez autoriser les utilisateurs à modifier leur profil dans votre application, vous utilisez un flux d’utilisateur de modification de profil.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux utilisateur (stratégies)** , puis sélectionnez **Nouveau flux d’utilisateur**.
1. Sélectionnez le flux d’utilisateur **Modification de profil** sous l’onglet **Recommandé**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *profileediting1*.
1. Sous **Fournisseurs d’identité**, sélectionnez **Connexion du compte local**.
1. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Nom d’affichage** et **Poste**. Cliquez sur **OK**.
1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
1. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Pour permettre aux utilisateurs de votre application de réinitialiser leur mot de passe, utilisez un flux utilisateur de réinitialisation de mot de passe.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux utilisateur (stratégies)** , puis sélectionnez **Nouveau flux d’utilisateur**.
1. Sélectionnez le flux d’utilisateur **Réinitialisation de mot de passe** sous l’onglet **Recommandé**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *passwordreset1*.
1. Sous **Fournisseurs d’identité**, activez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.
1. Sous Revendications d’application, cliquez sur **Afficher plus** et choisissez les revendications à renvoyer à votre application dans les jetons d’autorisation. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.
1. Cliquez sur **OK**.
1. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

Ensuite, découvrez comment ajouter des fournisseurs d’identité à vos applications pour permettre à vos utilisateurs de se connecter avec les fournisseurs que sont Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Ajouter des fournisseurs d’identité à vos applications >](tutorial-add-identity-providers.md)