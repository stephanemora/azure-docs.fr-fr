---
title: 'Tutoriel : Intégration d’Azure Active Directory avec CloudPassage | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d8bddc309db3ede67586a2067fcf57fbede7d6b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152788"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Tutoriel : Intégration d’Azure Active Directory avec CloudPassage

Dans ce didacticiel, vous allez apprendre à intégrer CloudPassage à Azure Active Directory (Azure AD).

L’intégration de CloudPassage dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à CloudPassage.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à CloudPassage (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec CloudPassage, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement CloudPassage pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de CloudPassage à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Ajout de CloudPassage à partir de la galerie
Pour configurer l’intégration de CloudPassage avec Azure AD, vous devez ajouter CloudPassage, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter CloudPassage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **CloudPassage**.

    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. Dans le volet de résultats, sélectionnez **CloudPassage**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec CloudPassage avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur CloudPassage équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur CloudPassage associé doit être établi.

Dans CloudPassage, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec CloudPassage, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test CloudPassage](#creating-a-cloudpassage-test-user)** pour avoir un équivalent de Britta Simon dans CloudPassage qui soit lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application CloudPassage.

**Pour configurer l’authentification unique Azure AD avec CloudPassage, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **CloudPassage**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. Dans la section **Domaine et URL CloudPassage**, procédez comme suit :

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://portal.cloudpassage.com/saml/init/accountid`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://portal.cloudpassage.com/saml/consume/accountid`. Vous pouvez obtenir la valeur de cet attribut en cliquant sur **SSO Setup documentation** dans la section **Single Sign-on Settings** de votre portail CloudPassage.

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique de CloudPassage](https://www.cloudpassage.com/company/contact/). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. Votre application CloudPassage attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre un exemple :
   
   ![Configure Single Sign-On](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration de CloudPassage** , cliquez sur **Configurer CloudPassage** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. Dans une autre fenêtre de navigateur Web, connectez-vous au site de votre entreprise CloudPassage en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Settings** puis sur **Site Administration**. 
   
    ![Configurer l'authentification unique][12]

1. Cliquez sur l’onglet **Authentication Settings** . 
   
    ![Configurer l'authentification unique][13]

1. Dans la section **Single Sign-On Settings** , procédez comme suit : 
   
    ![Configurer l'authentification unique][14]

    a. Cochez la case **Activer l’authentification unique (SSO) (Documentation de configuration de l’authentification unique)**.
    
    b. Collez **l’ID d’entité SAML** dans la zone de texte **URL de l’émetteur SAML**.
  
    c. Collez la valeur du champ **URL du service d’authentification unique SAML** dans la zone de texte **URL du point de terminaison SAML**.
  
    d. Collez la valeur du champ **URL de déconnexion** dans la zone de texte **Page de déconnexion**.
  
    e. Ouvrez votre certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat X 509**.
  
    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Création d’un utilisateur de test CloudPassage

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

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à CloudPassage.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à CloudPassage, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **CloudPassage**.

    ![Configurer l'authentification unique](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette CloudPassage dans le volet d’accès, vous devez être connecté automatiquement à votre application CloudPassage.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

