---
title: 'Didacticiel : Intégration d’Azure Active Directory à BambooHR | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 9e733aa9f609928f8129123c4286cfa98e51e1d4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549481"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Didacticiel : Intégration d’Azure Active Directory à BambooHR

Dans ce didacticiel, vous allez apprendre à intégrer BambooHR à Azure Active Directory (Azure AD).
L’intégration de BambooHR à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à BambooHR.
* Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à BambooHR (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à BambooHR, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement BambooHR pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* BambooHR prend en charge l’authentification unique (SSO) initiée par **SP**

## <a name="adding-bamboohr-from-the-gallery"></a>Ajout de BambooHR à partir de la galerie

Pour configurer l’intégration de BambooHR à Azure AD, vous devez ajouter BambooHR, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter BambooHR à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **BambooHR**, sélectionnez **BambooHR** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![BambooHR dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec BambooHR, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur BambooHR associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec BambooHR, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique BambooHR](#configure-bamboohr-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test BambooHR](#create-bamboohr-test-user)** pour avoir un équivalent de Britta Simon dans BambooHR lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec BambooHR, effectuez les étapes suivantes :

1. Dans le [Portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **BambooHR**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL BambooHR](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<company>.bamboohr.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `BambooHR-SAML`

    > [!NOTE]
    > La valeur du champ **URL de connexion** n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir la valeur, contactez [l’équipe de support de BambooHR](https://www.bamboohr.com/contact.php). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer BambooHR**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-bamboohr-single-sign-on"></a>Configurer l’authentification unique BambooHR

1. Dans une nouvelle fenêtre, connectez-vous à votre site d’entreprise BambooHR comme administrateur.

2. Dans la page d’accueil, effectuez les actions suivantes :
   
    ![Page Authentification unique BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Single Sign on")   

    a. Sélectionnez **Applications**.
   
    b. Dans le volet **Applications**, sélectionnez **Authentification unique**.
   
    c. Sélectionnez **Authentification unique SAML**.

3. Dans le volet **Authentification unique SAML**, effectuez les actions suivantes :
   
    ![Volet Authentification unique SAML](./media/bamboo-hr-tutorial/IC796692.png "Authentification unique SAML")
   
    a. Dans la zone **URL de connexion SSO**, collez **l’URL de connexion** que vous avez copiée à partir du Portail Azure à l’étape 6.
      
    b. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat X.509**.
   
    c. Sélectionnez **Enregistrer**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BambooHR.

1. Dans le Portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **BambooHR**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **BambooHR**.

    ![Lien BambooHR dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-bamboohr-test-user"></a>Créer un utilisateur de test BambooHR

Pour permettre aux utilisateurs Azure AD de se connecter à BambooHR, configurez-les manuellement dans BambooHR en effectuant les actions suivantes :

1. Connectez-vous à votre site **BambooHR** comme administrateur.

2. Dans la barre d’outils en haut, sélectionnez **Paramètres**.
   
    ![Le bouton Paramètres](./media/bamboo-hr-tutorial/IC796694.png "Paramètre")

3. Sélectionnez **Vue d’ensemble**.

4. Dans le volet gauche, sélectionnez **Sécurité** > **Utilisateurs**.

5. Tapez le nom d’utilisateur, le mot de passe et l’adresse e-mail du compte Azure AD valide que vous voulez configurer.

6. Sélectionnez **Enregistrer**.
        
>[!NOTE]
>Pour configurer des comptes d’utilisateur Azure AD, vous pouvez aussi utiliser les outils ou API de création de compte d’utilisateur de BambooHR.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette BambooHR dans le panneau d’accès doit vous connecter automatiquement à l’application BambooHR pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

