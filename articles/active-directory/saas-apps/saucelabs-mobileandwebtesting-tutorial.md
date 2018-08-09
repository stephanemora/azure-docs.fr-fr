---
title: 'Tutoriel : Intégration d’Azure Active Directory à Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sauce Labs - Mobile and Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 55d84256f408e80600308ede22dbaa903b070d90
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266884"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutoriel : Intégration d’Azure Active Directory à Sauce Labs - Mobile and Web Testing

Dans ce tutoriel, découvrez comment intégrer Sauce Labs - Mobile and Web Testing à Azure Active Directory (Azure AD).

L’intégration de Sauce Labs - Mobile and Web Testing à Azure AD vous procure les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Sauce Labs - Mobile and Web Testing.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Sauce Labs - Mobile and Web Testing (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sauce Labs - Mobile and Web Testing, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Sauce Labs - Mobile and Web Testing avec l’authentification unique activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Sauce Labs - Mobile and Web Testing à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Ajout de Sauce Labs - Mobile and Web Testing à partir de la galerie
Pour configurer l’intégration de Sauce Labs - Mobile and Web Testing dans Azure AD, vous devez ajouter Sauce Labs - Mobile and Web Testing à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Sauce Labs - Mobile and Web Testing à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Sauce Labs - Mobile and Web Testing**, sélectionnez **Sauce Labs - Mobile and Web Testing** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Sauce Labs - Mobile and Web Testing dans la liste des résultats](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Sauce Labs - Mobile and Web Testing grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Sauce Labs - Mobile and Web Testing équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Sauce Labs - Mobile and Web Testing associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Sauce Labs - Mobile and Web Testing, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Sauce Labs - Mobile and Web Testing](#create-a-sauce-labs---mobile-and-web-testing-test-user)** pour avoir un équivalent de Britta Simon dans Sauce Labs - Mobile and Web Testing, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Sauce Labs - Mobile and Web Testing.

**Pour configurer l’authentification unique Azure AD avec Sauce Labs - Mobile and Web Testing, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Sauce Labs - Mobile and Web Testing**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Dans la section **Domaine et URL de Sauce Labs - Mobile and Web Testing**, l’utilisateur n’aura pas à effectuer les étapes que l’application a déjà intégrées préalablement avec Azure.

    ![Informations d’authentification unique pour le domaine et les URL de Sauce Labs - Mobile and Web Testing](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre du navigateur web, ouvrez une session sur votre site d’entreprise Sauce Labs - Mobile and Web Testing en tant qu’administrateur.

7. Cliquez sur l’**icône Utilisateur** et sélectionnez l’onglet **Gestion des équipes**.

    ![Configurer l'authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Entrez votre **nom de domaine** dans la zone de texte.

    ![Configurer l'authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Cliquez sur l’onglet **Configurer**.

    ![Configurer l'authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. Dans la section **Configurer l’authentification unique**, procédez comme suit.

    ![Configurer l'authentification unique](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Cliquez sur **Parcourir** et chargez le fichier de métadonnées téléchargé à partir d’Azure AD.

    b. Cochez la case **AUTORISER L’APPROVISIONNEMENT JUSTE-À-TEMPS**.

    c. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Créer un utilisateur de test Sauce Labs - Mobile and Web Testing

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Sauce Labs - Mobile and Web Testing. Sauce Labs - Mobile and Web Testing prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Sauce Labs - Mobile and Web Testing s’il n’en existe pas encore.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sauce Labs - Mobile and Web Testing.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Sauce Labs - Mobile and Web Testing, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Sauce Labs - Mobile and Web Testing**.

    ![Lien Sauce Labs - Mobile and Web Testing dans la liste des applications](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Sauce Labs - Mobile and Web Testing dans le volet d’accès, vous êtes automatiquement connecté à l’application Sauce Labs - Mobile and Web Testing.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png