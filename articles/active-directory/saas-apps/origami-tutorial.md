---
title: 'Tutoriel : Intégration d’Azure Active Directory à Origami | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3587acd0232920e812512384af3158a1e0afbe
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191365"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutoriel : Intégration d’Azure Active Directory à Origami

Dans ce didacticiel, vous allez apprendre à intégrer Origami à Azure Active Directory (Azure AD).

L’intégration d’Origami à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Origami.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Origami (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Origami, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Origami pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Origami à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-origami-from-the-gallery"></a>Ajout d’Origami à partir de la galerie
Pour configurer l’intégration d’Origami à Azure AD, vous devez ajouter Origami à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Origami à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Origami**.

    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/tutorial_origami_search.png)

1. Dans le panneau des résultats, sélectionnez **Origami**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Origami avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Origami équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Origami associé doit être établie.

Dans Origami, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Origami, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Origami](#creating-an-origami-test-user)** pour obtenir un équivalent de Britta Simon dans Origami lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Origami.

**Pour configurer l’authentification unique Azure AD avec Origami, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Origami**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_samlbase.png)

1. Dans la section **Domaine et URL Origami**, procédez comme suit :

    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique d’Origami](https://wordpress.org/support/theme/origami). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/origami-tutorial/tutorial_origami_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration d’Origami**, cliquez sur **Configurer Origami** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_configure.png) 

1. Connectez-vous au compte Origami avec les droits d’administrateur.

1. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_51.png)

1. Dans la page de boîte de dialogue de configuration de l’authentification unique, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_531.png)

    a. Sélectionnez **Activer l’authentification unique**.

    b. Dans la zone de texte **URL de la page de connexion du fournisseur d’identité**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **URL de la page de déconnexion du fournisseur d’identité**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Parcourir** pour importer le certificat que vous avez téléchargé à partir du portail Azure.

    e. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/origami-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-origami-test-user"></a>Création d’un utilisateur de test Origami

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Origami. 

1. Connectez-vous au compte Origami avec les droits d’administrateur.

1. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_51.png)

1. Dans la boîte de dialogue **Utilisateurs et sécurité**, cliquez sur **Utilisateurs**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_54.png)

1. Cliquez sur **Add New User**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_55.png)

1. Dans la boîte de dialogue Ajouter un nouvel utilisateur, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_56.png)

    a. Dans la zone de texte **Nom d’utilisateur**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    b. Dans la zone de texte **Mot de passe** , entrez un mot de passe.

    c. Dans la zone de texte **Confirmer le mot de passe** , entrez de nouveau le mot de passe.

    d. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    e. Dans la zone de texte **Nom**, tapez le nom de l’utilisateur, par exemple **Simon**.

    f. Cliquez sur **Enregistrer**.
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_57.png)

1. Affectez des **Rôles d’utilisateur** et **l’Accès client** à l’utilisateur. 
   
    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Origami.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Origami, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Origami**.

    ![Configurer l'authentification unique](./media/origami-tutorial/tutorial_origami_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Origami dans le volet d’accès, vous devez être connecté automatiquement à votre application Origami.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

