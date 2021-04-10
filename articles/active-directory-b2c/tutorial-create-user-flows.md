---
title: Tutoriel – Créer des flux utilisateur – Azure Active Directory B2C
description: Ce tutoriel explique comment créer des flux utilisateur sur le portail Azure pour permettre l’inscription, la connexion et la modification de profil utilisateur pour vos applications dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42c6465af8e895d833332be847c134b97ee8ddc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781294"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutoriel : Créer des flux d’utilisateur dans Azure Active Directory B2C

Vos applications comportent peut-être des [flux utilisateur](user-flow-overview.md) qui permettent aux utilisateurs de s’inscrire, de se connecter ou de gérer leur profil. Vous pouvez créer plusieurs flux d’utilisateurs de types différents dans votre locataire Azure Active Directory B2C (Azure AD B2C) et les utiliser dans vos applications selon les besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Activer la réinitialisation du mot de passe libre-service
> * Créer un flux d’utilisateur de modification de profil


Ce tutoriel vous montre comment créer des flux d’utilisateur recommandés à l’aide du portail Azure. Si vous recherchez des informations sur la façon de configurer un flux ROPC (informations d’identification de mot de passe du propriétaire de la ressource) dans votre application, consultez [Configurer le flux des informations d’identification par mot de passe du propriétaire de ressource dans Azure AD B2C](add-ropc-policy.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Nous avons modifié la manière dont nous référençons les versions de flux utilisateur. Auparavant, nous proposions des versions V1 (prêtes pour la production) et des versions V1.1 et V2 (préversions). À présent, nous avons consolidé les flux utilisateur dans les versions **Recommandé** (préversion de nouvelle génération) et **Standard** (en disponibilité générale). Tous les flux utilisateur hérités V1.1 et V2 seront déconseillés d’ici le **1er août 2021**. Pour plus d’informations, consultez [Versions de flux utilisateur dans Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Prérequis

[Inscrivez vos applications](tutorial-register-applications.md) qui font partie des flux d’utilisateur à créer.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Le flux d’utilisateur Inscription et connexion gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs de votre application sont dirigés vers le chemin approprié en fonction du contexte.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez le répertoire qui contient votre locataire Azure AD B2C.

    ![Volet client B2C, répertoire et abonnement, Portail Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.

    ![Page Flux d'utilisateur du portail avec bouton Nouveau flux d'utilisateur en surbrillance](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**.

    ![Page Sélectionner un flux d'utilisateur avec Inscription et connexion en surbrillance](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)

    ![Page Créer un flux d'utilisateur du Portail Azure avec propriétés en surbrillance](./media/tutorial-create-user-flows/select-version.png)

1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *signupsignin1*.
1. Pour **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.
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

> [!NOTE]
> L’expérience « Exécuter le flux utilisateur » n’est actuellement pas compatible avec le type d’URL de réponse SPA utilisant le flux de code d’autorisation. Pour utiliser l’expérience « Exécuter le flux d’utilisateur » avec ces types d’applications, enregistrez une URL de réponse de type « Web » et activez le flux implicite, comme décrit [ici](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Activer la réinitialisation du mot de passe libre-service

Pour activer la [réinitialisation du mot de passe en libre-service](add-password-reset-policy.md) pour le flux d’utilisateur d’inscription ou de connexion :

1. Sélectionnez le flux d’utilisateur d’inscription ou de connexion que vous avez créé.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Complexité du mot de passe**, sélectionnez **Réinitialisation du mot de passe en libre-service**.
1. Sélectionnez **Enregistrer**.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Vous avez oublié votre mot de passe ?** .
1. Vérifiez l’adresse e-mail du compte que vous avez précédemment créé, puis sélectionnez **Continuer**.
1. Vous avez maintenant la possibilité de changer le mot de passe de l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Si vous voulez autoriser les utilisateurs à modifier leur profil dans votre application, vous utilisez un flux d’utilisateur de modification de profil.

1. Dans le menu de la page de vue d’ensemble du locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Modifications de profil**. 
1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**.
1. Entrez un **Nom** pour le flux d’utilisateur. Par exemple, *profileediting1*.
1. Sous **Fournisseurs d’identité**, sélectionnez **Connexion du compte local**.
2. Sous **Attributs utilisateur**, choisissez les attributs que le client peut modifier dans son profil. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Nom d’affichage** et **Poste**. Cliquez sur **OK**.
3. Cliquez sur **Créer** pour ajouter le flux utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Sélectionnez le flux utilisateur que vous avez créé pour ouvrir sa page de présentation, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec le compte que vous avez créé précédemment.
1. Vous avez désormais la possibilité de changer le nom d’affichage et le poste de l’utilisateur. Cliquez sur **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un flux d’utilisateur d’inscription et de connexion
> * Créer un flux d’utilisateur de modification de profil
> * Créer un flux d’utilisateur de réinitialisation du mot de passe

Ensuite, découvrez comment utiliser Azure AD B2C pour connecter et inscrire des utilisateurs dans une application. Suivez l’application web ASP.NET liée ci-dessous ou accédez à une autre application dans la table des matières, sous **Authentifier les utilisateurs**.

> [!div class="nextstepaction"]
> [Tutoriel : Activer l’authentification dans une application web à l’aide d’Azure AD B2C >](tutorial-web-app-dotnet.md)
