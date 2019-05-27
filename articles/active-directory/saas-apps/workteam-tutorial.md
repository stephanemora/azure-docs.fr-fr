---
title: "Didacticiel : Intégration d'Azure Active Directory à Workteam | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: a59e7d9177834790b6df0f1c45a60849d3fdca0e
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920298"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Didacticiel : Intégration d'Azure Active Directory à Workteam

Dans ce didacticiel, vous allez apprendre à intégrer Workteam à Azure Active Directory (Azure AD).
L’intégration de Workteam dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Workteam.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Workteam (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Workteam, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Workteam pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Workteam prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**

## <a name="adding-workteam-from-the-gallery"></a>Ajout de Workteam à partir de la galerie

Pour configurer l’intégration de Workteam à Azure AD, vous devez ajouter Workteam à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workteam à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Workteam**, sélectionnez **Workteam** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Workteam dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Workteam avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Workteam associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Workteam, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Workteam](#configure-workteam-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Workteam](#create-workteam-test-user)** pour avoir un équivalent de Britta Simon dans Workteam lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Workteam, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Workteam**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le  **fournisseur d’identité** , l’utilisateur n’a rien à faire, car l’application est pré-intégrée avec Azure.

    ![Informations d’authentification unique dans la section Workteam Domain and URLs (Domaine et URL Workteam)](common/preintegrated.png)

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Workteam Domain and URLs (Domaine et URL Workteam)](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL : `https://app.workte.am`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Workteam**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-workteam-single-sign-on"></a>Configurer l’authentification unique Workteam

1. Dans une autre fenêtre de navigateur web, connectez-vous à Workteam en tant qu’administrateur de la sécurité.

2. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Organization settings** (Paramètres de l’organisation). 

    ![Paramètres de Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. Dans la section **AUTHENTICATION** (Authentification), cliquez sur **l’icône Paramètres**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. Sur la page **SAML Settings** , procédez comme suit :

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Sous **SAML IdP** (Fournisseur d’identité SAML), sélectionnez **AD Azure**.

    b. Dans la zone de texte **URL du service d’authentification unique SAML**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du Portail Azure.

    c. Dans la zone de texte **ID d’entité SAML**, collez l’**Identificateur Azure AD** que vous avez copié dans le Portail Azure.

    d. Dans le Bloc-notes, ouvrez le **certificat codé en base 64** téléchargé à partir du Portail Azure, copiez son contenu, puis collez-le dans la zone **SAML Signing Certificate (Base64)** (Certificat de signature SAML (Base64)).

    e. Cliquez sur **OK**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workteam.

1. Dans le Portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Workteam**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Workteam**.

    ![Lien Workteam dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-workteam-test-user"></a>Créer un utilisateur de test Workteam

Pour se connecter à Workteam, les utilisateurs d’Azure AD doivent être approvisionnés dans Workteam. Dans Workteam, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Workteam en tant qu’administrateur de la sécurité.

2. En haut et au milieu de la page **Organization settings** (Paramètres de l’organisation), cliquez sur **USERS** (Utilisateurs), puis cliquez sur **NEW USER** (Nouvel utilisateur).

    ![Utilisateur Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Dans la page **New employee** (Nouvel employé), effectuez les étapes suivantes :

    ![Nouvel utilisateur Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Dans la zone de texte **Name** (Nom), saisissez le prénom de l’utilisateur, par exemple **Brittasimon**.

    b. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    c. Cliquez sur **OK**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Workteam dans le panneau d’accès doit vous connecter automatiquement à l’application Workteam pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

