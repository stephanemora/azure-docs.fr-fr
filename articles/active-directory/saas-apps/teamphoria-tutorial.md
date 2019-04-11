---
title: "Didacticiel : Intégration d'Azure Active Directory à Teamphoria | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277202"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Didacticiel : Intégration d'Azure Active Directory à Teamphoria

Dans ce didacticiel, vous allez apprendre à intégrer Teamphoria à Azure Active Directory (Azure AD).
L’intégration de Teamphoria dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Teamphoria.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Teamphoria (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Teamphoria, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement Teamphoria pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Teamphoria prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-teamphoria-from-the-gallery"></a>Ajout de Teamphoria à partir de la galerie

Pour configurer l’intégration de Teamphoria à Azure AD, vous devez ajouter Teamphoria à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Teamphoria à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Teamphoria**, sélectionnez **Teamphoria** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Teamphoria dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Teamphoria avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Teamphoria associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Teamphoria, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Teamphoria](#configure-teamphoria-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Teamphoria](#create-teamphoria-test-user)** pour avoir un équivalent de Britta Simon dans Teamphoria qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Teamphoria, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Teamphoria**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Teamphoria](common/sp-intiated.png)

    Dans la zone de texte **URL de connexion**, entrez une URL au format suivant :  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Vous devez mettre à jour cette valeur avec l’URL de connexion réelle. Contactez l[’équipe de support technique Teamphoria](https://www.teamphoria.com/) pour obtenir l’URL de connexion. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Teamphoria**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-teamphoria-single-sign-on"></a>Configurer l’authentification unique Teamphoria

1. Pour configurer l’authentification unique côté **Teamphoria**, connectez-vous à votre application Teamphoria en tant qu’administrateur.

1. Accédez à l’option **ADMIN SETTINGS** (Paramètres d’administration) dans la barre d’outils gauche puis, sous l’onglet Configure (Configurer), cliquez sur **SINGLE SIGN-ON** (Authentification unique) pour ouvrir la fenêtre de configuration de l’authentification unique (SSO).

    ![Configurer l'authentification unique](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Cliquez sur l’option **ADD NEW IDENTITY PROVIDER** (Ajouter un nouveau fournisseur d’identité) dans le coin supérieur droit pour ouvrir le formulaire permettant d’ajouter les paramètres pour l’authentification unique.

    ![Configurer l'authentification unique](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Entrez les informations dans les champs comme indiqué ci-dessous :

    ![Configurer l'authentification unique](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOM D'AFFICHAGE** : entrez le nom d'affichage du plug-in sur la page d'administration.

    b. **NOM DU BOUTON** : nom de l'onglet qui apparaîtra sur la page de connexion via l'authentification unique.

    c. **CERTIFICAT** : dans le Bloc-notes, ouvrez le certificat téléchargé précédemment à partir du portail Azure, copiez le contenu de celui-ci, puis collez-le dans ce champ.

    d. **POINT D'ENTRÉE** : Collez l’**URL de connexion** copiée précédemment à partir du portail Azure.

    e. Définissez l’option sur **ON** , puis cliquez sur **SAVE** (Enregistrer).

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Teamphoria.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Teamphoria**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Teamphoria**.

    ![Lien Teamphoria dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-teamphoria-test-user"></a>Créer un utilisateur de test Teamphoria

Pour se connecter à Teamphoria, les utilisateurs d’Azure AD doivent être provisionnés dans Teamphoria. Dans le cas de Teamphoria, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Teamphoria en tant qu’administrateur.

1. Cliquez sur les paramètres **ADMIN** dans la barre d’outils gauche puis, sous l’onglet **MANAGE** (Gérer), cliquez sur **USERS** (Utilisateurs) afin d’ouvrir la page d’administration pour les utilisateurs.

    ![Ajouter un employé](./media/teamphoria-tutorial/admin_manage_users.png)

1. Cliquez sur l’option **MANUAL INVITE** (Inviter manuellement).

    ![Inviter des personnes](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Dans cette page, effectuez l’action suivante.

    ![Inviter des personnes](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Dans la zone de texte **EMAIL ADDRESS**, entrez l’**adresse e-mail** de l’utilisateur, par exemple BrittaSimon.

    b. Dans la zone de texte **FIRST NAME**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **LAST NAME**, tapez le nom de l’utilisateur, par exemple **Simon**.

    d. Cliquez sur **INVITE 1 USER** (Inviter l’utilisateur 1). L’utilisateur doit accepter l’invitation pour être créé dans le système.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Teamphoria dans le volet d’accès, vous devez être connecté automatiquement à l’application Teamphoria pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

