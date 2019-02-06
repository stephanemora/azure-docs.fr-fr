---
title: 'Tutoriel : Intégration d’Azure Active Directory à Yodeck | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 2f767e813c2ba803cfafaaf6d9798ee2ea3f93d2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165096"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutoriel : Intégration d’Azure Active Directory à Yodeck

Dans ce tutoriel, vous allez découvrir comment intégrer Yodeck à Azure Active Directory (Azure AD).

L’intégration de Yodeck avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Yodeck
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Yodeck (par authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Yodeck, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Yodeck pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Yodeck à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-yodeck-from-the-gallery"></a>Ajout de Yodeck à partir de la galerie
Pour configurer l’intégration de Yodeck avec Azure AD, vous devez ajouter Yodeck, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Yodeck à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Yodeck**, sélectionnez **Yodeck** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Yodeck dans la liste des résultats](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Yodeck, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Yodeck équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Yodeck associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Yodeck, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Yodeck](#create-a-yodeck-test-user)** pour avoir un équivalent de Britta Simon dans Yodeck lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Yodeck.

**Pour configurer l’authentification unique Azure AD avec Yodeck, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Yodeck**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

1. Dans la section **Domaines et URL Yodeck**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Yodeck](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    Dans la zone de texte **Identificateur (ID d’entité)**, tapez l’URL suivante : `https://app.yodeck.com/api/v1/account/metadata/`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Yodeck](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.yodeck.com/login`

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/yodeck-tutorial/tutorial_general_400.png)
    
1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Yodeck en tant qu’administrateur.

1. Cliquez sur **User Settings** dans l’angle supérieur droit de la page et sélectionnez **Account Settings**.

    ![Configuration de Yodeck](./media/yodeck-tutorial/configure1.png)

1. Sélectionnez **SAML** et effectuez les opérations suivantes :

    ![Configuration de Yodeck](./media/yodeck-tutorial/configure2.png)

    a. Sélectionnez **Import from URL**.

    b. Dans la zone de texte **URL**, collez la valeur **URL des métadonnées de fédération de l’application** que vous avez copiée dans le portail Azure, puis cliquez sur **Import**.
    
    c. Une fois l’**URL des métadonnées de fédération de l’application** importée, les champs restants sont renseignés automatiquement.

    d. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/yodeck-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/yodeck-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/yodeck-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/yodeck-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-yodeck-test-user"></a>Créer un utilisateur de test Yodeck

Pour se connecter à Yodeck, les utilisateurs d’Azure AD doivent être provisionnés dans Yodeck.
Dans le cas de Yodeck, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Yodeck en tant qu’administrateur.

1. Cliquez sur **User Settings** dans l’angle supérieur droit de la page et sélectionnez **Users**.

    ![Ajouter un employé](./media/yodeck-tutorial/user1.png)

1. Cliquez sur **+User** pour ouvrir l’onglet **User Details**.

    ![Ajouter un employé](./media/yodeck-tutorial/user2.png)

1. Dans la page de boîte de dialogue **User Details** , procédez comme suit :

    ![Ajouter un employé](./media/yodeck-tutorial/user3.png)

    a. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com**.

    d. Sélectionnez l’option **Account Permissions** conforme aux exigences de votre organisation.
    
    e. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Yodeck.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Yodeck, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

1. Dans la liste des applications, sélectionnez **Yodeck**.

    ![Lien Yodeck dans la liste des applications](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Yodeck dans le volet d’accès, vous devez être connecté automatiquement à votre application Yodeck.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

