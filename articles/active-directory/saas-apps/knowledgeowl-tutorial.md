---
title: 'Didacticiel : Intégration d’Azure Active Directory à KnowledgeOwl | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c86ad6e4b11f21c648083fac35e15eec7658c1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995515"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Tutoriel : Intégration d'Azure Active Directory avec KnowledgeOwl

Dans ce didacticiel, vous allez apprendre à intégrer KnowledgeOwl à Azure Active Directory (Azure AD).

L’intégration de KnowledgeOwl dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à KnowledgeOwl.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à KnowledgeOwl (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à KnowledgeOwl, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement KnowledgeOwl pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de KnowledgeOwl à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-knowledgeowl-from-the-gallery"></a>Ajout de KnowledgeOwl à partir de la galerie
Pour configurer l’intégration de KnowledgeOwl à Azure AD, vous devez ajouter KnowledgeOwl à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter KnowledgeOwl à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **KnowledgeOwl**, sélectionnez **KnowledgeOwl** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![KnowledgeOwl dans la liste des résultats](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec KnowledgeOwl avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur KnowledgeOwl équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur associé dans KnowledgeOwl doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec KnowledgeOwl, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test KnowledgeOwl](#create-a-knowledgeowl-test-user)** pour avoir un équivalent de Britta Simon dans KnowledgeOwl, lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application KnowledgeOwl.

**Pour configurer l'authentification unique Azure AD avec KnowledgeOwl, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **KnowledgeOwl**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Dans la section **Domaine et URL Help KnowledgeOwl**, suivez les étapes ci-dessous pour configurer l’application en mode initié par le **fournisseur d’identité** :

    ![Informations d’authentification unique dans Domaine et URL KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant :

    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez changer cette valeur pour l’identificateur, l’URL de réponse et l’URL de connexion réels. Ceci est expliqué plus loin dans le tutoriel.

1. Votre application KnowledgeOwl attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications.

    ![Configurer l'authentification unique](./media/knowledgeowl-tutorial/attribute.png)

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut | Espace de noms|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.
    
    ![Configure Single Sign-On](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d.Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.
    
    e. Cliquez sur **OK**.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration de KnowledgeOwl**, cliquez sur **Configurer KnowledgeOwl** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise KnowledgeOwl en tant qu’administrateur.

1. Cliquez sur **Paramètres**, puis sélectionnez **Sécurité**.

     ![Configuration KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Faites défiler jusqu’à **Intégration de l'authentification unique SAML** et effectuez les étapes suivantes :
    
     ![Configuration KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

     a. Sélectionnez **Enable SAML SSO** (Activer l’authentification unique SAML).

     b. Copiez la valeur de l’**ID d’entité SP** et collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Domaine et URL KnowledgeOwl** du portail Azure.

     c. Copiez la valeur de l’**URL de connexion SP** et collez-la dans la zone de texte **URL de connexion et URL de réponse** de la section **Domaine et URL KnowledgeOwl** du portail Azure.

     d. Dans la zone de texte **ID d’entité IdP**, collez la valeur **ID d’entité SAML** que vous avez copiée sur le portail Azure.

     e. Dans la zone de texte **IdP Login URL** (URL de connexion de fournisseur d’identité), collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

     f. Dans la zone de texte **IdP Logout URL** (URL de déconnexion de fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

     g. Chargez le certificat téléchargé à partir du portail Azure en cliquant sur **Upload IdP Certificate (Charger un certificat IdP)**.

     h. Cliquez sur **Map SAML Attributes (Mapper les attributs SAML)** pour mapper les attributs, puis procédez comme suit :
    
     ![Configuration KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Dans la zone de texte **SSO ID** (ID d’authentification unique), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`.
    * Dans la zone de texte **Username/Email** (Nom d’utilisateur/E-mail), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    * Dans la zone de texte **First Name** (Prénom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    * Dans la zone de texte **Last Name** (Prénom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    * Cliquez sur **Enregistrer**.

      i. Cliquez sur **Enregistrer** au bas de la page.

      ![Configuration KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Créer un utilisateur de test KnowledgeOwl

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans KnowledgeOwl. KnowledgeOwl prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé durant une tentative d’accès à KnowledgeOwl.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez  [l’équipe de support technique KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à KnowledgeOwl.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à KnowledgeOwl, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **KnowledgeOwl**.

    ![Lien KnowledgeOwl dans la liste des applications](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque KnowledgeOwl dans le volet d’accès, vous devez être connecté automatiquement à votre application KnowledgeOwl.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

