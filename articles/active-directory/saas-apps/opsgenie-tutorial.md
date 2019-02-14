---
title: 'Didacticiel : Intégration d’Azure Active Directory à OpsGenie | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9858fc38698ae2c5bd272a3494bcf02bce2d8e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194595"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutoriel : Intégration d’Azure Active Directory à OpsGenie

Dans ce didacticiel, vous allez apprendre à intégrer OpsGenie à Azure Active Directory (Azure AD).

L’intégration d’OpsGenie dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OpsGenie.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à OpsGenie (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à OpsGenie, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OpsGenie pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de OpsGenie à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-opsgenie-from-the-gallery"></a>Ajout de OpsGenie à partir de la galerie
Pour configurer l’intégration de OpsGenie à Azure AD, vous devez ajouter OpsGenie à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter OpsGenie à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **OpsGenie**.

    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. Dans le panneau des résultats, sélectionnez **OpsGenie**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec OpsGenie, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur OpsGenie correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur OpsGenie associé doit être établie.

Dans OpsGenie, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec OpsGenie, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test OpsGenie](#creating-a-opsgenie-test-user)** pour obtenir un équivalent de Britta Simon dans OpsGenie lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application OpsGenie.

**Pour configurer l’authentification unique Azure AD avec OpsGenie, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **OpsGenie**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Dans la section **Domaine et URL OpsGenie**, procédez comme suit :

    ![Configurer l'authentification unique](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.opsgenie.com/auth/login`

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration d’OpsGenie**, cliquez sur **Configurer OpsGenie** pour ouvrir la fenêtre **Configurer l’authentification**. Dans la section Référence rapide, copiez **l’URL du service d’authentification unique SAML**.

    ![Configurer l'authentification unique](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Ouvrez une autre instance de navigateur, puis connectez-vous à OpsGenie en tant qu’administrateur.

1. Cliquez sur **Paramètres**, puis cliquez sur l’onglet **Authentification unique**.
   
    ![Authentification unique OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Pour activer l’authentification unique, sélectionnez **Activé**.
   
    ![Paramètres OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. Dans la section **Fournisseur** cliquez sur l’onglet **Azure Active Directory**.
   
    ![Paramètres OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Sur la page de boîte de dialogue Azure Active Directory, procédez comme suit :
   
    ![Paramètres OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Dans la zone de texte **Point de terminaison SAML 2.0**, collez la valeur **URL du service d’authentification unique** que vous avez copiée sur le Portail Azure.
    
    b. Dans la zone de texte **URL des métadonnées**, collez la valeur **URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.
    
    c. Cliquez sur **Enregistrer les modifications**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-opsgenie-test-user"></a>Création d’un utilisateur de test OpsGenie

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OpsGenie. 

1. Dans une fenêtre de navigateur web, connectez-vous à votre client OpsGenie en tant qu’administrateur.

1. Accédez à la liste Utilisateurs en cliquant sur **Utilisateur** dans le volet gauche.
   
   ![Paramètres OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Cliquez sur **Add User**.

1. Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :
   
   ![Paramètres OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. Dans la zone de texte **E-mail**, saisissez l’adresse e-mail de BrittaSimon utilisée dans Azure Active Directory.
   
   b. Dans la zone de texte **Nom complet**, tapez **Britta Simon**.
   
   c. Cliquez sur **Enregistrer**. 

>[!NOTE]
>Britta reçoit un e-mail contenant des instructions pour configurer son profil.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OpsGenie.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à OpsGenie, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **OpsGenie**.

    ![Configurer l'authentification unique](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette OpsGenie dans le volet d’accès, vous devez être connecté automatiquement à votre application OpsGenie.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png

