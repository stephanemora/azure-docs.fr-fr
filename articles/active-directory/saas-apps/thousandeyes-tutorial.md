---
title: 'Didacticiel : Intégration d’Azure Active Directory à ThousandEyes | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bb78b014ffe2d40b9a61da8e47893056e435ddc6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088658"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Didacticiel : Intégration d’Azure AD à ThousandEyes

Dans ce didacticiel, vous allez apprendre à intégrer ThousandEyes à Azure Active Directory (Azure AD).
L’intégration de ThousandEyes à Azure AD vous fait bénéficier des avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à ThousandEyes.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à ThousandEyes (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ThousandEyes, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement ThousandEyes pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ThousandEyes prend en charge l’authentification unique lancée par le **fournisseur de services**

* ThousandEyes prend en charge le [provisionnement **automatique** d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Ajout de ThousandEyes à partir de la galerie

Pour configurer l’intégration de ThousandEyes à Azure AD, vous devez ajouter ThousandEyes à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ThousandEyes à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **ThousandEyes**, sélectionnez **ThousandEyes** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![ThousandEyes dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de ThousandEyes avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur ThousandEyes associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec ThousandEyes, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique ThousandEyes](#configure-thousandeyes-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test ThousandEyes](#create-thousandeyes-test-user)** pour avoir un équivalent de Britta Simon dans ThousandEyes lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de ThousandEyes, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ThousandEyes**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL ThousandEyes](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL : `https://app.thousandeyes.com/login/sso`

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer ThousandEyes**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-thousandeyes-single-sign-on"></a>Configurer l’authentification unique ThousandEyes

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **ThousandEyes** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/thousandeyes-tutorial/ic790066.png "Paramètres")

3. Cliquez sur **Account**

    ![Compte](./media/thousandeyes-tutorial/ic790067.png "Compte")

4. Cliquez sur l’onglet **Security & Authentication (Sécurité et authentification)** .

    ![Sécurité et authentification](./media/thousandeyes-tutorial/ic790068.png "Sécurité et authentification")

5. Dans la section **Setup Single Sign-On** , procédez comme suit :

    ![Configurer l’authentification unique](./media/thousandeyes-tutorial/ic790069.png "Configurer l’authentification unique")

    a. Sélectionnez **Activer l'authentification unique**.

    b. Dans la zone de texte **Login Page URL** (URL de page de connexion), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Logout Page URL** (URL de page de déconnexion), collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez la valeur de l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    e. Dans **Certificat de vérification**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.

    f. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez brittasimon@yourcompanydomain.extension. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ThousandEyes.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **ThousandEyes**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **ThousandEyes**.

    ![Lien ThousandEyes dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-thousandeyes-test-user"></a>Créer un utilisateur de test ThousandEyes

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ThousandEyes. ThousandEyes prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez [ici](thousandeyes-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ThousandEyes en tant qu’administrateur.

2. Cliquez sur **Settings**.

    ![Paramètres](./media/thousandeyes-tutorial/IC790066.png "Paramètres")

3. Cliquez sur **Account**.

    ![Compte](./media/thousandeyes-tutorial/IC790067.png "Compte")

4. Cliquez sur l’onglet **Accounts & Users (Comptes et utilisateurs)** .

    ![Comptes et utilisateurs](./media/thousandeyes-tutorial/IC790073.png "Comptes et utilisateurs")

5. Dans la section **Add Users & Accounts (Ajouter des utilisateurs et des comptes)** , procédez comme suit :

    ![Ajouter des comptes d’utilisateurs](./media/thousandeyes-tutorial/IC790074.png "Ajouter des comptes d’utilisateurs")

    a. Dans la zone de texte **Name**, tapez le nom complet d’un utilisateur, par exemple **Britta Simon**.

    b. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple brittasimon@contoso.com.

    b. Cliquez sur le bouton **Add New User to Account**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un courrier électronique contenant un lien permettant de confirmer et activer le compte.

> [!NOTE]
> Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur, fournis par ThousandEyes, pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ThousandEyes dans le volet d’accès, vous devez être connecté automatiquement à l’application ThousandEyes pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
