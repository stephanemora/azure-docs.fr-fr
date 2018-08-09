---
title: 'Didacticiel : Intégration d’Azure Active Directory à QuickHelp | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et QuickHelp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: c99be60301085dddfd5c658ee1eed81b88238e54
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421585"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Didacticiel : intégration d’Azure Active Directory à QuickHelp

Dans ce didacticiel, vous allez apprendre à intégrer QuickHelp avec Azure Active Directory (Azure AD).

L’intégration de QuickHelp dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à QuickHelp.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à QuickHelp (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à QuickHelp, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement QuickHelp pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de QuickHelp à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-quickhelp-from-the-gallery"></a>Ajout de QuickHelp à partir de la galerie
Pour configurer l’intégration de QuickHelp à Azure AD, vous devez ajouter QuickHelp à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter QuickHelp à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **QuickHelp**.

    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/tutorial_quickhelp_search.png)

1. Dans le volet de résultats, sélectionnez **QuickHelp**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec QuickHelp sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur QuickHelp équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur QuickHelp associé doit être établie.

Dans QuickHelp, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec QuickHelp, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test QuickHelp](#creating-a-quickhelp-test-user)** pour avoir un équivalent de Britta Simon dans QuickHelp lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application QuickHelp.

**Pour configurer l’authentification unique Azure AD avec QuickHelp, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **QuickHelp**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

1. Dans la section **Domaine et URL QuickHelp**, procédez comme suit :

    ![Configurer l'authentification unique](./media/quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://quickhelp.com/<ROUTEURL>`

    b. Dans la zone de texte **Identificateur**, tapez une URL : `https://auth.quickhelp.com`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’administrateur QuickHelp de votre organisation ou votre BrainStorm Client Success Manager pour obtenir la valeur.
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/quickhelp-tutorial/tutorial_general_400.png) 

1. Connectez-vous à votre site d’entreprise QuickHelp en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Configurer l'authentification unique][21]

1. Dans le menu **QuickHelp Admin** (Administration de QuickHelp), cliquez sur **Settings** (Paramètres).
   
    ![Configurer l'authentification unique][22]

1. Cliquez sur **Authentication Settings**.

1. Dans la page **Authentication Settings** (Paramètres d’authentification), procédez comme suit :
   
    ![Configurer l'authentification unique][23]
   
    a. Pour **SSO Type**, sélectionnez **WSFederation**.
   
    b. Pour charger votre fichier de métadonnées Azure téléchargé, cliquez sur **Browse**, accédez au fichier, puis cliquez sur **Upload Metadata**.
   
    c. Dans la zone de texte **Email** (E-mail), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
   
    d. Dans la zone de texte **First Name** (Prénom), tapez `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
   
    e. Dans la zone de texte **Last Name** (Nom), tapez `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
   
    f. Dans la **barre d’actions**, cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/quickhelp-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-quickhelp-test-user"></a>Création d’un utilisateur de test QuickHelp

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans QuickHelp.
Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur QuickHelp équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur QuickHelp associé doit être établie.

QuickHelp prend en charge l’approvisionnement juste-à-temps. Cela signifie que, si nécessaire, un compte d’utilisateur est automatiquement créé dans QuickHelp et qu’il est lié au compte Azure AD.

Vous n’avez aucune opération à effectuer dans cette section.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à QuickHelp.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à QuickHelp, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **QuickHelp**.

    ![Configurer l'authentification unique](./media/quickhelp-tutorial/tutorial_quickhelp_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Quand vous cliquez sur la vignette QuickHelp dans le volet d’accès, vous devez être connecté automatiquement à votre application QuickHelp.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png
