---
title: Tutoriel - Créer des flux d’utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment créer des flux d’utilisateur pour vos applications dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76d56b17973c04992a8855917c814ced649bd0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418839"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Didacticiel : Créer des flux d’utilisateur dans Azure Active Directory B2C

Dans vos applications, vous pouvez avoir des [flux d’utilisateur](active-directory-b2c-reference-policies.md) qui permettent aux utilisateurs de s’inscrire, de se connecter ou de gérer leur profil. Vous pouvez créer plusieurs flux d’utilisateur de types différents dans votre locataire Azure Active Directory (Azure AD) B2C et les utiliser dans vos applications selon vos besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

Ce tutoriel vous montre comment créer des flux d’utilisateur recommandés à l’aide du portail Azure. Si vous cherchez des informations sur la configuration d’un flux ROPC (informations d’identification de mot de passe du propriétaire de la ressource) dans votre application, consultez [Configurer le flux des informations d’identification de mot de passe du propriétaire de la ressource dans Azure AD B2C](configure-ropc.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

[Inscrivez vos applications](tutorial-register-applications.md) qui font partie des flux d’utilisateur à créer. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Le flux d’utilisateur Inscription et connexion gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs de votre application sont dirigés vers le chemin approprié en fonction du contexte.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.

    ![Basculer vers l’annuaire de l’abonnement](./media/tutorial-create-user-flows/switch-directories.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans le menu de gauche, sélectionnez **Flux d’utilisateur**, puis **Nouveau flux d’utilisateur**.

    ![Sélectionnez Nouveau flux utilisateur](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Sélectionnez le flux d’utilisateur **Inscription et connexion** sous l’onglet Recommandé.

    ![Sélectionner le flux d’utilisateur Inscription et connexion](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
7. Pour **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.

    ![Définir les propriétés de flux](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Pour **Attributs utilisateur et revendications**, choisissez les revendications et les attributs à collecter et envoyer par l’utilisateur pendant l’inscription. Par exemple, sélectionnez **Afficher plus**, puis choisissez **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

    ![Sélectionner des attributs et des revendications](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Dans la page Vue d’ensemble du flux d’utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
2. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
3. Cliquez sur **Exécuter le flux d’utilisateur**, puis sélectionnez **S’inscrire maintenant**.

    ![Exécuter le flux d’utilisateur](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Entrez une adresse e-mail valide, cliquez sur **Envoyer un code de vérification**, puis entrez le code de vérification que vous recevez.
5. Entrez un nouveau mot de passe et confirmez-le.
6. Entrez le nom que vous voulez afficher, sélectionnez votre pays et votre région, entrez un code postal, puis cliquez sur **Créer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.
7. Vous pouvez maintenant réexécuter le flux d’utilisateur et vous connecter avec le compte que vous avez créé. Le jeton retourné comprend les revendications que vous avez sélectionnées pour le nom, le pays et le code postal.

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Si vous voulez autoriser les utilisateurs à modifier leur profil dans votre application, vous utilisez un flux d’utilisateur de modification de profil.

1. Dans le menu de gauche, sélectionnez **Flux d’utilisateur**, puis **Nouveau flux d’utilisateur**.
2. Sélectionnez le flux d’utilisateur **Modification de profil** sous l’onglet Recommandé.
3. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *profileediting1*.
4. Sous **Fournisseurs d’identité**, sélectionnez **Connexion du compte local**.
5. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Par exemple, sélectionnez **Afficher plus**, puis choisissez **Nom d’affichage** et **Poste**. Cliquez sur **OK**.
6. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Dans la page Vue d’ensemble du flux d’utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
2. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
3. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
4. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Vous pouvez autoriser l’utilisateur de votre application à réinitialiser son mot de passe si nécessaire. Pour activer la réinitialisation de mot de passe, vous utilisez un flux d’utilisateur de réinitialisation de mot de passe.

1. Dans le menu de gauche, sélectionnez **Flux d’utilisateur**, puis **Nouveau flux d’utilisateur**.
2. Sélectionnez le flux d’utilisateur **Réinitialisation de mot de passe** sous l’onglet Recommandé.
3. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *passwordreset1*.
4. Sous **Fournisseurs d’identité**, activez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**.
5. Sous Revendications d’application, cliquez sur **Afficher plus** et choisissez les revendications à renvoyer à votre application dans les jetons d’autorisation. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.
6. Cliquez sur **OK**.
7. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Dans la page Vue d’ensemble du flux d’utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
2. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
3. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
4. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

> [!div class="nextstepaction"]
> [Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](tutorial-add-identity-providers.md)