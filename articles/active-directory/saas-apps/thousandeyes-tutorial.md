---
title: 'Didacticiel : Intégration d’Azure Active Directory à ThousandEyes | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d85c4dbd3cc1c3d827a3f8324e63f75eb942425
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892980"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutoriel : Intégration d’Azure AD à ThousandEyes

Dans ce didacticiel, vous allez apprendre à intégrer ThousandEyes à Azure Active Directory (Azure AD).

L’intégration de ThousandEyes à Azure AD vous fait bénéficier des avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ThousandEyes.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ThousandEyes (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à ThousandEyes, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ThousandEyes pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [Offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ThousandEyes à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-thousandeyes-from-the-gallery"></a>Ajout de ThousandEyes à partir de la galerie
Pour configurer l’intégration de ThousandEyes à Azure AD, vous devez ajouter ThousandEyes à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ThousandEyes à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **ThousandEyes**.

    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. Dans le volet de résultats, sélectionnez **ThousandEyes**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de ThousandEyes avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ThousandEyes équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ThousandEyes associé doit être établie.

Dans ThousandEyes, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ThousandEyes, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test ThousandEyes](#creating-a-thousandeyes-test-user)** pour avoir un équivalent de Britta Simon dans ThousandEyes lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ThousandEyes.

**Pour configurer l’authentification unique Azure AD auprès de ThousandEyes, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **ThousandEyes**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configurer l'authentification unique](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Dans la section **Domaine et URL ThousandEyes**, procédez comme suit :

    ![Configurer l'authentification unique](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://app.thousandeyes.com/login/sso`

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de ThousandEyes**, cliquez sur **Configurer ThousandEyes** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **ThousandEyes** en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/thousandeyes-tutorial/ic790066.png "Paramètres")

1. Cliquez sur **Account**

    ![Compte](./media/thousandeyes-tutorial/ic790067.png "Compte")

1. Cliquez sur l’onglet **Security & Authentication (Sécurité et authentification)**.

    ![Sécurité et authentification](./media/thousandeyes-tutorial/ic790068.png "Sécurité et authentification")

1. Dans la section **Setup Single Sign-On** , procédez comme suit :

    ![Configurer l’authentification unique](./media/thousandeyes-tutorial/ic790069.png "Configurer l’authentification unique")

    a. Sélectionnez **Activer l'authentification unique**.

    b. Dans la zone de texte **URL de la page de connexion**, collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **URL de la page de déconnexion**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez l’**ID d’entité SAML** copié à partir du portail Azure.

    e. Dans **Certificat de vérification**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure.

    f. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.

    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="creating-a-thousandeyes-test-user"></a>Création d’un utilisateur de test ThousandEyes

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ThousandEyes. ThousandEyes prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez [ici](thousandeyes-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ThousandEyes en tant qu’administrateur.

1. Cliquez sur **Settings**.

    ![Paramètres](./media/thousandeyes-tutorial/IC790066.png "Paramètres")

1. Cliquez sur **Account**.

    ![Compte](./media/thousandeyes-tutorial/IC790067.png "Compte")

1. Cliquez sur l’onglet **Accounts & Users (Comptes et utilisateurs)**.

    ![Comptes et utilisateurs](./media/thousandeyes-tutorial/IC790073.png "Comptes et utilisateurs")

1. Dans la section **Add Users & Accounts (Ajouter des utilisateurs et des comptes)**, procédez comme suit :

    ![Ajouter des comptes d’utilisateurs](./media/thousandeyes-tutorial/IC790074.png "Ajouter des comptes d’utilisateurs")

    a. Dans la zone de texte **Name**, tapez le nom complet d’un utilisateur, par exemple **Britta Simon**.

    b. Dans **E-mail** zone de texte, tapez l’adresse e-mail de l’utilisateur comme **brittasimon\@contoso.com**.

    b. Cliquez sur le bouton **Add New User to Account**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un courrier électronique contenant un lien permettant de confirmer et activer le compte.

> [!NOTE]
> Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur, fournis par ThousandEyes, pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ThousandEyes.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à ThousandEyes, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **ThousandEyes**.

    ![Configurer l'authentification unique](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette ThousandEyes dans le volet d’accès, vous devez vous connecter automatiquement à votre application ThousandEyes.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
