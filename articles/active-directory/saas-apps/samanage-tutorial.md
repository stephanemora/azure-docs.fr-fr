---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Samanage | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Samanage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 56018ff0be07a48cf9448b9b92de5694ebac18bc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543514"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Didacticiel : Intégration d’Azure Active Directory à Samanage

Dans ce didacticiel, vous allez apprendre à intégrer Samanage avec Azure Active Directory (Azure AD).
L’intégration de Samanage à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Samanage.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Samanage (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à Samanage, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Samanage pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Samanage prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-samanage-from-the-gallery"></a>Ajout de Samanage à partir de la galerie

Pour configurer l’intégration de Samanage à Azure AD, vous devez ajouter Samanage à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Samanage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Samanage**, sélectionnez **Samanage** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Samanage dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Samanage sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Samanage associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Samanage, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Samanage](#configure-samanage-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Samanage](#create-samanage-test-user)** pour avoir un équivalent de Britta Simon dans Samanage qui soit lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Samanage, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Samanage**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Samanage](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeur avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel. Pour plus de détails, contactez l’[équipe de support technique Samanage](https://www.samanage.com/support). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Samanage**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-samanage-single-sign-on"></a>Configurer l’authentification unique Samanage

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Samanage en tant qu’administrateur.

2. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation de gauche.
   
    ![Tableau de bord](./media/samanage-tutorial/tutorial_samanage_001.png "tableau de bord")

3. Cliquez sur **Single Sign-On**.
   
    ![Authentification unique](./media/samanage-tutorial/tutorial_samanage_002.png "Single Sign on")

4. Accédez à la section **Login using SAML** (Connexion avec SAML), puis procédez comme suit :
   
    ![Connexion avec SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 
    a. Cliquez sur **Enable Single Sign-On with SAML**(Activer l’authentification unique avec SAML).  
 
    b. Dans la zone de texte **Identity Provider URL** (URL du fournisseur d’identité), collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure.    
 
    c. Vérifiez que l’URL de connexion (**Login URL**) correspond à l’**URL de connexion** de la section **Configuration SAML de base** dans le portail Azure.
 
    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur d’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
 
    e. Dans la zone de texte **SAML Issuer** (Émetteur SAML), saisissez l’URI d’ID d’application défini dans votre fournisseur d’identité.
 
    f. Ouvrez votre certificat codé en base 64 téléchargé à partir du portail Azure dans Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Paste your Identity Provider x.509 Certificate below** (Collez le certificat X.509 de votre fournisseur d’identité ci-dessous).
 
    g. Cliquez sur **Create users if they do not exist in Samanage**(Créer des utilisateurs s’ils n’existent pas dans Samanage).
 
    h. Cliquez sur **Update**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Samanage.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Samanage**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Samanage**.

    ![Lien Samanage dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-samanage-test-user"></a>Créer un utilisateur de test Samanage

Pour permettre aux utilisateurs Azure AD de se connecter à Samanage, vous devez les approvisionner dans Samanage.  
Dans le cas de Samanage, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Samanage en tant qu’administrateur.

2. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation gauche.
   
    ![Paramétrage](./media/samanage-tutorial/tutorial_samanage_001.png "Programme d’installation")

3. Cliquez sur l’onglet **Users** .
   
    ![Utilisateurs](./media/samanage-tutorial/tutorial_samanage_006.png "Utilisateurs")

4. Cliquez sur **New User**.
   
    ![Nouvel utilisateur](./media/samanage-tutorial/tutorial_samanage_007.png "Nouvel utilisateur")

5. Entrez le **Nom** et l’**Adresse e-mail** du compte Azure Active Directory que vous souhaitez approvisionner, puis cliquez sur **Create user** (Créer un utilisateur).
   
    ![Create User](./media/samanage-tutorial/tutorial_samanage_008.png "Create User") (Créer un utilisateur)
   
   >[!NOTE]
   >Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Samanage pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Samanage dans le panneau d’accès doit vous connecter automatiquement à l’application Samanage pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

