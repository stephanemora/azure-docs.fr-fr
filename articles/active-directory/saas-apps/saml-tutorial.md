---
title: 'Didacticiel : Intégration d’Azure Active Directory à l’application métier avec jeton SAML 1.1 activé | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et l’application métier avec jeton SAML 1.1 activé.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: edabc09f820093d088ec0b8ed1222fb26c800bee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426426"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Didacticiel : Intégration d’Azure Active Directory à l’application métier avec jeton SAML 1.1 activé

Dans ce didacticiel, vous allez apprendre à intégrer l’application métier avec jeton SAML 1.1 activé à Azure Active Directory (Azure AD).

L’intégration de l’application métier avec jeton SAML 1.1 activé dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à l’application métier avec jeton SAML 1.1 activé.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à l’application métier avec jeton SAML 1.1 activé (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à l’application métier avec jeton SAML 1.1 activé, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement avec authentification unique pour l’application métier avec jeton SAML 1.1 activé

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’application métier avec jeton SAML 1.1 activé à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Ajout d’application métier avec jeton SAML 1.1 activé à partir de la galerie
Pour configurer l’intégration de l’application métier avec jeton SAML 1.1 activé à Azure AD, vous devez ajouter l’application métier avec jeton SAML 1.1 activé à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter l’application métier avec jeton SAML 1.1 activé à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, saisissez **Application métier avec jeton SAML 1.1 activé**, sélectionnez **Application métier avec jeton SAML 1.1 activé** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![L’application métier avec jeton SAML 1.1 activé dans la liste des résultats](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec l’application métier avec jeton SAML 1.1 activé grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur de l’application métier avec jeton SAML 1.1 activé équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur de l’application métier avec jeton SAML 1.1 activé associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec l’application métier avec jeton SAML 1.1 activé, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test d’application métier avec jeton SAML 1.1 activé](#create-a-saml-11-token-enabled-lob-app-test-user)** pour avoir un équivalent de Britta Simon dans l’application métier avec jeton SAML 1.1 activé lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application métier avec jeton SAML 1.1 activé.

**Pour configurer l’authentification unique Azure AD avec l’application métier avec jeton SAML 1.1 activé, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de **l’Application métier avec jeton SAML 1.1 activé**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/saml-tutorial/tutorial_saml_samlbase.png)

1. Dans la section **Domaine et URL d’application métier avec jeton SAML 1.1 activé**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL d’application métier avec jeton SAML 1.1 activé](./media/saml-tutorial/tutorial_saml_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://your-app-url`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://your-app-url`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Remplacez ces valeurs par les URL spécifiques des applications.  

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/saml-tutorial/tutorial_saml_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/saml-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration d’application métier avec jeton SAML 1.1 activé**, cliquez sur **Configurer l’application métier avec jeton SAML 1.1 activé** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de l’application métier avec jeton SAML 1.1 activé](./media/saml-tutorial/tutorial_saml_configure.png) 

1. Pour configurer l’authentification unique côté **Application métier avec jeton SAML 1.1 activé**, vous devez envoyer le **Certificat (Base64) téléchargé, l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à l’équipe de support de l’application. Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/saml-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/saml-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/saml-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Créer un utilisateur de test d’application métier avec jeton SAML 1.1 activé

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans l’application métier avec jeton SAML 1.1 activé. Travaillez avec l’équipe du support d’application pour créer un utilisateur du côté application. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à l’application métier avec jeton SAML 1.1 activé.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à l’application métier avec jeton SAML 1.1 activé, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Application métier avec jeton SAML 1.1 activé**.

    ![Le lien de l’application métier avec jeton SAML 1.1 activé dans la liste des applications](./media/saml-tutorial/tutorial_saml_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque de l’application métier avec jeton SAML 1.1 activé dans le volet d’accès, vous devez être connecté automatiquement à votre application métier avec jeton SAML 1.1 activé.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
