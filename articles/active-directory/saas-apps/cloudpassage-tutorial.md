---
title: 'Tutoriel : Intégration d’Azure Active Directory avec CloudPassage | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45d5e0cfe39436b9af3a2b671a194bce88b91720
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883485"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Didacticiel : Intégration d’Azure Active Directory avec CloudPassage

Dans ce didacticiel, vous allez apprendre à intégrer CloudPassage à Azure Active Directory (Azure AD).
L’intégration de CloudPassage dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à CloudPassage.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à CloudPassage (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec CloudPassage, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement CloudPassage pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* CloudPassage prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-cloudpassage-from-the-gallery"></a>Ajout de CloudPassage à partir de la galerie

Pour configurer l’intégration de CloudPassage avec Azure AD, vous devez ajouter CloudPassage, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter CloudPassage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **CloudPassage**, sélectionnez **CloudPassage** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![CloudPassage dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec CloudPassage avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur CloudPassage associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec CloudPassage, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique CloudPassage](#configure-cloudpassage-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test CloudPassage](#create-cloudpassage-test-user)** pour avoir un équivalent de Britta Simon dans CloudPassage qui soit lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec CloudPassage, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **CloudPassage**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL CloudPassage](common/sp-reply.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://portal.cloudpassage.com/saml/init/accountid`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://portal.cloudpassage.com/saml/consume/accountid`. Vous pouvez obtenir la valeur de cet attribut en cliquant sur **SSO Setup documentation** dans la section **Single Sign-on Settings** de votre portail CloudPassage.

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique de CloudPassage](https://www.cloudpassage.com/company/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application CloudPassage attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit : 

    | Nom | Attribut source|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |

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

8. Dans la section **Configurer CloudPassage**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-cloudpassage-single-sign-on"></a>Configurer l’authentification unique CloudPassage

1. Dans une autre fenêtre de navigateur Web, connectez-vous au site de votre entreprise CloudPassage en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Settings** puis sur **Site Administration**. 
   
    ![Configurer l'authentification unique][12]

1. Cliquez sur l’onglet **Authentication Settings** . 
   
    ![Configurer l'authentification unique][13]

1. Dans la section **Single Sign-On Settings** , procédez comme suit : 
   
    ![Configurer l'authentification unique][14]

    a. Cochez la case **Activer l’authentification unique (SSO) (Documentation de configuration de l’authentification unique)**.
    
    b. Collez la valeur **Identificateur Azure AD** dans la zone de texte **SAML issuer URL** (URL de l’émetteur SAML).
  
    c. Collez la valeur **URL de connexion** dans la zone de texte **SAML endpoint URL** (URL du point de terminaison SAML).
  
    d. Collez la valeur **URL de déconnexion** dans la zone de texte **Logout landing page** (Page de déconnexion).
  
    e. Ouvrez votre certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat X 509**.
  
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
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à CloudPassage.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **CloudPassage**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **CloudPassage**.

    ![Lien CloudPassage dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-cloudpassage-test-user"></a>Créer un utilisateur de test CloudPassage

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans CloudPassage.

**Pour créer un utilisateur appelé Britta Simon dans CloudPassage, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **CloudPassage** en tant qu’administrateur. 

1. Dans la barre d’outils située en haut, cliquez sur **Paramètres**, puis sur **Administration du site**. 
   
    ![Création d’un utilisateur de test CloudPassage][22] 

1. Cliquez sur l’onglet **Users**, puis sur **Add a user**. 
   
    ![Création d’un utilisateur de test CloudPassage][23]

1. Dans la section **Add New User** , procédez comme suit : 
   
    ![Création d’un utilisateur de test CloudPassage][24]
    
    a. Dans la zone de texte **First Name** , tapez Britta. 
  
    b. Dans la zone de texte **Last Name** , tapez Simon.
  
    c. Dans les zones de texte **Username**, **Email** et **Retype Email**, entrez le nom d’utilisateur de Britta dans Azure AD.
  
    d. En tant que **Type d’accès**, sélectionnez **Activer l’accès portail Halo**.
  
    e. Cliquez sur **Add**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette CloudPassage dans le volet d’accès, vous devez être connecté automatiquement à l’application CloudPassage pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

