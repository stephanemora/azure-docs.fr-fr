---
title: 'Didacticiel : Intégration d’Azure Active Directory à Syncplicity | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 11ac9d859ec76c782f762dfc6f424d70ea487665
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360226"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Didacticiel : Intégration d’Azure Active Directory à Syncplicity

Dans ce didacticiel, vous allez apprendre à intégrer Syncplicity à Azure Active Directory (Azure AD).
L’intégration de Syncplicity dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Syncplicity.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Syncplicity (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Syncplicity, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Syncplicity pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Syncplicity prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-syncplicity-from-the-gallery"></a>Ajout de Syncplicity à partir de la galerie

Pour configurer l’intégration de Syncplicity à Azure AD, vous devez ajouter Syncplicity à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Syncplicity à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Syncplicity**, sélectionnez **Syncplicity** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Syncplicity dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Syncplicity à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Syncplicity associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Syncplicity, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Syncplicity](#configure-syncplicity-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Syncplicity](#create-syncplicity-test-user)** pour avoir un équivalent de Britta Simon dans Syncplicity lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Syncplicity, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Syncplicity**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Syncplicity](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.syncplicity.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Syncplicity](https://www.syncplicity.com/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Syncplicity**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-syncplicity-single-sign-on"></a>Configurer l’authentification unique Syncplicity

1. Connectez-vous à votre locataire **Syncplicity** .

1. Dans le menu en haut, cliquez sur **admin**, sélectionnez **settings**, puis cliquez sur **Custom domain and single sign-on**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Dans la boîte de dialogue **Single Sign on (SSO)** , procédez comme suit :

    ![Authentification unique \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Dans la zone de texte **Custom Domain** , entrez le nom de votre domaine.
  
    b. Sélectionnez **Enabled** dans **Single Sign-On Status**.

    c. Dans la zone de texte **Entity Id** (ID d’entité), collez l’**Identificateur Azure AD** que vous avez copié dans le portail Azure.

    d. Dans la zone de texte **Sign-in page URL** (URL de la page de connexion), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Logout page URL** (URL de la page de déconnexion), collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans **Certificat du fournisseur d’identité**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.

    g. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Syncplicity.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Syncplicity**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Syncplicity**.

    ![Lien Syncplicity dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.
    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-syncplicity-test-user"></a>Créer un utilisateur de test Syncplicity

Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Syncplicity. Cette section décrit comment créer des comptes d’utilisateur AAD dans Syncplicity.

**Pour approvisionner un compte d’utilisateur dans Syncplicity, procédez comme suit :**

1. Connectez-vous à votre locataire **Syncplicity** (par exemple : `https://company.Syncplicity.com`).

1. Cliquez sur **admin** et sélectionnez **user accounts** (comptes d’utilisateur), puis cliquez sur **ADD A USER** (Ajouter un utilisateur).

    ![Gestion des utilisateurs](./media/syncplicity-tutorial/ic769764.png "Gestion des utilisateurs")

1. Entrez l’**adresse e-mail** d’un compte Azure AD que vous souhaitez provisionner, sélectionnez **User** (Utilisateur) pour **Role**, puis cliquez sur **NEXT** (SUIVANT).

    ![Informations du compte](./media/syncplicity-tutorial/ic769765.png "Informations du compte")

    > [!NOTE]
    > Le détenteur du compte AAD reçoit un message électronique contenant un lien pour confirmer et activer le compte.

1. Sélectionnez un groupe dans votre société dont votre nouvel utilisateur doit devenir membre, puis cliquez sur **SUIVANT**.

    ![Appartenance de groupe](./media/syncplicity-tutorial/ic769772.png "Appartenance de groupe")

    > [!NOTE]
    > Si aucun groupe n’est répertorié, cliquez sur **SUIVANT**.

1. Sélectionnez les dossiers que vous souhaitez placer sous le contrôle de Syncplicity sur l’ordinateur de l’utilisateur, puis cliquez sur **SUIVANT**.

    ![Dossiers Syncplicity](./media/syncplicity-tutorial/ic769773.png "Dossiers Syncplicity")

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Syncplicity, pour approvisionner des comptes utilisateur AAD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Syncplicity dans le panneau d’accès doit vous connecter automatiquement à l’application Syncplicity pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
