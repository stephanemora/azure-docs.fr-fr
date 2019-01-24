---
title: 'Tutoriel : Intégration d’Azure Active Directory à EthicsPoint Incident Management (EPIM) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et EthicsPoint Incident Management (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: c000d75dfc0cfd398fdb0156add4d94fdfe641e4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808661"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Tutoriel : Intégration d’Azure Active Directory à EthicsPoint Incident Management (EPIM)

Dans ce didacticiel, vous allez apprendre à intégrer EthicsPoint Incident Management (EPIM) à Azure Active Directory (Azure AD).

L’intégration dEthicsPoint Incident Management (EPIM) avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à EthicsPoint Incident Management (EPIM)
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à EthicsPoint Incident Management (EPIM) (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à EthicsPoint Incident Management (EPIM), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement EthicsPoint Incident Management (EPIM) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’EthicsPoint Incident Management (EPIM) à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Ajout d’EthicsPoint Incident Management (EPIM) à partir de la galerie
Pour configurer l’intégration d’EthicsPoint Incident Management (EPIM) avec Azure AD, vous devez ajouter EthicsPoint Incident Management (EPIM), à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter EthicsPoint Incident Management (EPIM) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **EthicsPoint Incident Management (EPIM)**.

    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_search.png)

1. Dans le volet des résultats, sélectionnez **EthicsPoint Incident Management (EPIM)**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec EthicsPoint Incident Management (EPIM) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur EthicsPoint Incident Management (EPIM) équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur EthicsPoint Incident Management (EPIM) associé doit être établi.

Dans EthicsPoint Incident Management (EPIM), assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec EthicsPoint Incident Management (EPIM), vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test EthicsPoint Incident Management (EPIM)](#creating-a-ethicspoint-incident-management-epim-test-user)** pour avoir un équivalent de Britta Simon dans EthicsPoint Incident Management (EPIM) lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application EthicsPoint Incident Management (EPIM).

**Pour configurer l’authentification unique Azure AD avec EthicsPoint Incident Management (EPIM), procédez comme suit :**

1. Sur le portail Azure, à la page d’intégration de l’application **EthicsPoint Incident Management (EPIM)**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_samlbase.png)

1. Dans la section **Domaine et URL EthicsPoint Incident Management (EPIM)**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse, l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de prise en charge des clients EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/ethicspoint-incident-management-tutorial/tutorial_general_400.png)
    
1. Pour configurer l’authentification unique du côté d’**EthicsPoint Incident Management (EPIM)**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à l’[équipe de support technique d’EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-ethicspoint-incident-management-epim-test-user"></a>Création d’un utilisateur de test d’EthicsPoint Incident Management (EPIM)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans EthicsPoint Incident Management (EPIM). Travaillez avec l’[équipe de support technique d’EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) pour ajouter les utilisateurs dans la plateforme EthicsPoint Incident Management (EPIM).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EthicsPoint Incident Management (EPIM).

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à EthicsPoint Incident Management (EPIM), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **EthicsPoint Incident Management (EPIM)**.

    ![Configurer l'authentification unique](./media/ethicspoint-incident-management-tutorial/tutorial_ethicspoint_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Lorsque vous cliquez sur la mosaïque EthicsPoint Incident Management (EPIM) dans le volet d’accès, vous devez être connecté automatiquement à votre application EthicsPoint Incident Management (EPIM).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_04.png

[100]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_201.png
[202]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_202.png
[203]: ./media/ethicspoint-incident-management-tutorial/tutorial_general_203.png

