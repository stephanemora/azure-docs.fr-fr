---
title: 'Didacticiel : Intégration d’Azure Active Directory à Boomi | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 9be16f7ce1fbe37eb823a33e4f8cf1b29acc4fd6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810429"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutoriel : Intégration d’Azure Active Directory à Boomi

Dans ce didacticiel, vous allez apprendre à intégrer Boomi à Azure Active Directory (Azure AD).
L’intégration de Boomi dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Boomi.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Boomi (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Boomi, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Boomi pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Boomi prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-boomi-from-the-gallery"></a>Ajout de Boomi depuis la galerie

Pour configurer l’intégration de Boomi avec Azure AD, vous devez ajouter Boomi disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Boomi à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Boomi**, sélectionnez **Boomi** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Boomi dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Boomi avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Boomi associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Boomi, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Boomi](#configure-boomi-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Boomi](#create-boomi-test-user)** pour avoir un équivalent de Britta Simon dans Boomi lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Boomi, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Boomi**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Informations d’authentification unique dans Domaine et URL Boomi](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://platform.boomi.com/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Boomi](https://boomi.com/company/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Boomi attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | NOM |  Attribut source|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

8. Dans la section **Configurer Boomi**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-boomi-single-sign-on"></a>Configurer l’authentification unique Boomi

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur. 

2. Accédez à **Nom de la société** et allez dans **Configurer**.

3. Cliquez sur l’onglet **Options SSO** et suivez les étapes ci-dessous.

    ![Configurer l’authentification unique côté application](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Cochez la case **Enable SAML Single Sign-On**.

    b. Cliquez sur **Importer** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat du fournisseur d’identité**.

    c. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), copiez la valeur de **URL de connexion** indiquée dans la fenêtre Configuration de l’application Azure AD.

    d. Dans **Federation Id Location** (Emplacement ID de fédération), sélectionnez le bouton radio **Federation Id is in FEDERATION_ID Attribute element** (L’ID de fédération se trouve dans l’élément d’attribut FEDERATION_ID).

    e. Cliquez sur le bouton **Enregistrer** .

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Boomi.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Boomi**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Boomi**.

    ![Lien Boomi dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-boomi-test-user"></a>Créer un utilisateur de test Boomi

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les approvisionner dans Boomi. En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

2. Après connexion, accédez à **Gestion des utilisateurs** et allez dans **Utilisateurs**.

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_001.png "Utilisateurs")

3. Cliquez sur l’icône **+**, et la boîte de dialogue **Ajouter/gérer les rôles utilisateur** s’ouvre.

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_002.png "Utilisateurs")

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_003.png "Utilisateurs")

    a. Dans la zone de texte **Adresse e-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple BrittaSimon@contoso.com.

    b. Dans la zone de texte **Prénom**, tapez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom**, tapez le nom de l’utilisateur, par exemple Simon.

    d. Entrez **l’ID de fédération** de l’utilisateur. Chaque utilisateur doit posséder un ID de fédération qui l’identifie de façon unique dans le compte.

    e. Affectez le rôle **Utilisateur standard** à l’utilisateur. Ne lui affectez pas le rôle d’administrateur, car cela lui donnerait l’accès normal à AtomSphere, ainsi que l’accès en authentification unique.

    f. Cliquez sur **OK**.

    > [!NOTE]
    > L’utilisateur ne recevra pas d’e-mail de notification de bienvenue contenant un mot de passe pouvant être utilisé pour se connecter au compte AtomSphere, car son mot de passe est géré par le biais du fournisseur d’identité. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Boomi dans le panneau d’accès doit vous connecter automatiquement à l’application Boomi pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

