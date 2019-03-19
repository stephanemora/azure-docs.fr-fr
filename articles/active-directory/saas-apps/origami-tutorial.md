---
title: 'Tutoriel : Intégration d’Azure Active Directory à Origami | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 1a3e01b7275b7d8329a9fc3bfc90e20398fdf38b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845092"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Didacticiel : Intégration d’Azure Active Directory à Origami

Dans ce didacticiel, vous allez apprendre à intégrer Origami à Azure Active Directory (Azure AD).
L’intégration d’Origami à Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à Origami.
* Vous pouvez autoriser vos utilisateurs à être automatiquement connecté à Origami (Single Sign-On) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Origami, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement origami l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Prend en charge de origami **SP** initiée par l’authentification unique

## <a name="adding-origami-from-the-gallery"></a>Ajout d’Origami à partir de la galerie

Pour configurer l’intégration d’Origami à Azure AD, vous devez ajouter Origami à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Origami à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Origami**, sélectionnez **Origami** dans le volet de résultats puis cliquez sur **ajouter** pour ajouter l’application.

     ![Origami dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurer et tester sur l’authentification unique de l’Azure AD avec Origami avec un utilisateur de test appelé **Britta Simon**.
Pour l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Origami associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Origami, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer Origami Single Sign-On](#configure-origami-single-sign-on)**  : pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer utilisateur de test Origami](#create-origami-test-user)**  - pour avoir un équivalent de Britta Simon dans Origami lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer Azure AD-authentification unique avec Origami, procédez comme suit :

1. Dans le [Azure portal](https://portal.azure.com/), dans le **Origami** page d’intégration d’application, sélectionnez **Single sign-on**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification dans un seul domaine et URL origami](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique d’Origami](https://wordpress.org/support/theme/origami). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Sur le **configurer Origami** section, copiez l’URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-origami-single-sign-on"></a>Configurer Origami Single Sign-On

1. Connectez-vous au compte Origami avec les droits d’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_51.png)

3. Dans la page de boîte de dialogue de configuration de l’authentification unique, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_531.png)

    a. Sélectionnez **Activer l’authentification unique**.

    b. Dans le **Sign-in Page URL du fournisseur d’identité** zone de texte, collez la valeur de **URL de connexion**, que vous avez copiée à partir du portail Azure.

    c. Dans le **URL de Page de déconnexion du fournisseur d’identité** zone de texte, collez la valeur de **URL de déconnexion**, que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Parcourir** pour importer le certificat que vous avez téléchargé à partir du portail Azure.

    e. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Origami.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Origami**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Origami**.

    ![Le lien Origami dans la liste des Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-origami-test-user"></a>Créer utilisateur de test Origami

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Origami. 

1. Connectez-vous au compte Origami avec les droits d’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_51.png)

3. Dans la boîte de dialogue **Utilisateurs et sécurité**, cliquez sur **Utilisateurs**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_54.png)

4. Cliquez sur **Add New User**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_55.png)

5. Dans la boîte de dialogue Ajouter un nouvel utilisateur, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_56.png)

    a. Dans le **nom d’utilisateur** zone de texte, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    b. Dans la zone de texte **Mot de passe** , entrez un mot de passe.

    c. Dans la zone de texte **Confirmer le mot de passe** , entrez de nouveau le mot de passe.

    d. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    e. Dans la zone de texte **Nom**, tapez le nom de l’utilisateur, par exemple **Simon**.

    f. Cliquez sur **Enregistrer**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_57.png)

6. Affectez des **Rôles d’utilisateur** et **l’Accès client** à l’utilisateur. 
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Origami dans le volet d’accès, vous devez être automatiquement connecté à l’Origami pour lequel vous configurez l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

