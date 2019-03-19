---
title: "Tutoriel : Intégration d'Azure Active Directory à Pingboard | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26418a5159781eab96cf7730d669ccb65742d86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861483"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutoriel : Intégration d'Azure Active Directory à Pingboard

Ce didacticiel explique comment intégrer Pingboard avec Azure Active Directory (Azure AD).

L’intégration de Pingboard avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pingboard
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Pingboard (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Pingboard, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pingboard pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pingboard à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Ajout de Pingboard à partir de la galerie
Pour configurer l’intégration de Pingboard avec Azure AD, vous devez ajouter Pingboard, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Pingboard à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications d’entreprise][2]

1. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Pingboard**, sélectionnez **Pingboard** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Pingboard dans la liste des résultats](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pingboard sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pingboard équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Pingboard associé.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pingboard.

Pour configurer et tester l’authentification unique Azure AD avec Pingboard, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Pingboard](#create-a-pingboard-test-user)** - pour avoir un équivalent de Britta Simon dans Pingboard lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Pingboard.

**Pour configurer l’authentification unique Azure AD auprès de Pingboard, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Pingboard**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1.  Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. Dans la section **Domaines et URL Pingboard**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**:

    ![DIP d’informations d’authentification unique dans Domaine et URL Pingboard](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `http://app.pingboard.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Fournisseurs de service d’informations d’authentification unique dans Domaine et URL Pingboard](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     Dans la zone de texte **URL de connexion**, tapez l’URL au format suivant : `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Notez que ces valeurs ne sont pas réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique Pingboard](https://support.pingboard.com/).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Métadonnées XML de Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/pingboard-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique côté Pingboard, ouvrez une nouvelle fenêtre de navigateur et connectez-vous à votre compte Pingboard. Pour configurer l’authentification unique, vous devez être un administrateur Pingboard.

1. Dans le menu supérieur, sélectionnez **Applications > Intégrations**

    ![Configurer l'authentification unique](./media/pingboard-tutorial/Pingboard_integration.png)

1. Sur la page **Intégrations**, recherchez la mosaïque **« Azure Active Directory »**, puis cliquez dessus.

    ![Intégration de l’authentification unique Pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

1. Dans la fenêtre modale qui suit, cliquez sur **« Configurer »**

    ![Bouton de configuration Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

1. Sur la page suivante, vous remarquez que « l’intégration Azure SSO est activée ». Ouvrez le fichier XML de métadonnées téléchargé dans un bloc-notes puis collez le contenu dans **IDP Metadata** (métadonnées du fournisseur d’identité).

    ![Écran de configuration SSO de Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. Le fichier est validé et, si tout est correct, l’authentification unique est alors activée.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/pingboard-tutorial/create_aaduser_02.png)

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.

    ![Bouton Ajouter](./media/pingboard-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/pingboard-tutorial/create_aaduser_04.png)

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-pingboard-test-user"></a>Créer un utilisateur de test Pingboard

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Pingboard. Pingboard prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Des informations supplémentaires sur la configuration de l’attribution automatique d’utilisateurs sont disponibles [ici](pingboard-provisioning-tutorial.md).

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous au site d’entreprise Pingboard en tant qu’administrateur.

1. Cliquez sur le bouton **« Ajouter un employé »** sur la page **Annuaire**.

    ![Ajouter un employé](./media/pingboard-tutorial/create_testuser_add.png)

1. Dans la boîte de dialogue **Ajouter un employé** , procédez comme suit :

    ![Inviter des personnes](./media/pingboard-tutorial/create_testuser_name.png)

    a. Dans la zone de texte **Nom complet**, tapez le nom complet d’un utilisateur, par exemple, **Britta Simon**.

    b. Dans le **E-mail** zone de texte, tapez l’adresse de messagerie de l’utilisateur comme **brittasimon\@contoso.com**.

    c. Dans la zone de texte **Poste**, tapez le poste de Britta Simon.

    d. Dans la liste déroulante **Emplacement**, sélectionnez l’emplacement de Britta Simon.

    e. Cliquez sur **Add**.

1. Un écran de confirmation s’affiche pour confirmer l’ajout de l’utilisateur.

    ![Confirmer](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pingboard.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Pingboard, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Pingboard**.

    ![Lien correspondant à Pingboard dans la liste Applications](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

Lorsque vous cliquez sur la mosaïque Pingboard dans le volet d’accès, vous devez être connecté automatiquement à votre application Pingboard.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png
