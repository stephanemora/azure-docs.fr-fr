---
title: 'Didacticiel : Intégration d’Azure Active Directory à Trello | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 730ff5ff12f18d1f85b3ca53adb42fee41e19fb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088289"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Didacticiel : Intégration d’Azure Active Directory à Trello

Dans ce didacticiel, vous allez apprendre à intégrer Trello à Azure Active Directory (Azure AD).
L’intégration de Trello dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Trello.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Trello (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Trello, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Trello pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Trello prend en charge l’authentification unique lancée par le fournisseur de services et le fournisseur d’identité

* Trello prend en charge le provisionnement d’utilisateurs juste-à-temps

## <a name="add-trello-from-the-gallery"></a>Ajouter Trello depuis la galerie

Pour configurer l’intégration de Trello avec Azure AD, ajoutez d’abord Trello à partir de la galerie à votre liste d’applications SaaS managées.

Pour ajouter Trello à partir de la galerie, effectuez les étapes suivantes :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Trello**, puis sélectionnez **Trello** dans le volet de résultats.

5. Sélectionnez le bouton **Ajouter** pour ajouter l’application.

     ![Trello dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Trello pour un utilisateur de test nommé **Britta Simon**.

Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Trello associé.

Pour configurer et tester l’authentification unique Azure AD avec Trello, vous devez suivre les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Configurer l’authentification unique Trello](#configure-trello-single-sign-on) pour configurer les paramètres de l’authentification unique côté application.
3. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Créer un utilisateur de test Trello](#create-a-trello-test-user) pour avoir un équivalent de Britta Simon dans Trello lié à la représentation Azure AD associée.
6. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

> [!NOTE]
> Vous devez obtenir le champ de données dynamiques **\<enterprise\>** de Trello. Si vous ne connaissez pas la valeur de ce champ, contactez l’[équipe du support Trello](mailto:support@trello.com) pour obtenir la valeur correspondant à votre entreprise.

Pour configurer l’authentification unique Azure AD avec Trello, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Trello**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le fournisseur d’identité, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Trello](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://trello.com/auth/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, entrez une URL au format suivant : `https://trello.com/auth/saml/consume/<enterprise>`

5. Sélectionnez **Définir des URL supplémentaires**, puis effectuez les étapes suivantes si vous souhaitez configurer l’application en mode lancé par le fournisseur de services :

    ![Informations d’authentification unique dans Domaine et URL Trello](common/metadata-upload-additional-signon.png)

    Dans la zone **URL de connexion**, entrez une URL au format suivant : `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support Trello](mailto:support@trello.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application Trello attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Boîte de dialogue Attributs utilisateur](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez l’attribut de jeton SAML comme illustré dans l’image précédente. Ensuite, effectuez les étapes suivantes :

    | Nom |  Attribut source|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Sélectionnez **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Boîte de dialogue des revendications des utilisateurs](common/new-save-attribute.png)

    ![Gérer les revendications des utilisateurs](common/new-attribute-details.png)

    b. Dans la zone de texte **Nom**, entrez le nom d’attribut affiché pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Dans **Source**, sélectionnez **Attribut**.

    e. Dans la liste **Attribut de la source**, entrez la valeur d’attribut affichée pour cette ligne.

    f. Sélectionnez **OK**.

    g. Sélectionnez **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le **certificat (Base64)** dont vous avez besoin parmi les options proposées. Enregistrez ensuite le certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Configurer Trello**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-trello-single-sign-on"></a>Configurer l’authentification unique Trello

Pour configurer l’authentification unique côté Trello, envoyez d’abord le **certificat (Base64)** téléchargé et les URL copiées depuis le portail Azure à l’[équipe du support Trello](mailto:support@trello.com). L’équipe vérifiera que l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans la boîte de dialogue **Utilisateur**, procédez comme suit.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom de l’utilisateur**, entrez « brittasimon@yourcompanydomain.extension ». Par exemple, ici, entrez « BrittaSimon@contoso.com ».

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur figurant dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Trello.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Trello**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Trello**.

    ![Lien Trello dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Sélectionnez le bouton **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs. Sélectionnez ensuite le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Sélectionnez ensuite le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="create-a-trello-test-user"></a>Créer un utilisateur de test Trello

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Trello. Trello prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Trello, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’ [équipe du support Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du portail MyApps.

Le fait de sélectionner la vignette Trello dans le portail MyApps doit vous connecter automatiquement à l’application Trello. Pour plus d’informations sur le portail MyApps, consultez [Qu’est-ce que le portail MyApps ?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

