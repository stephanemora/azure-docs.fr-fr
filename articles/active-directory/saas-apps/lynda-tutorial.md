---
title: 'Tutoriel : Intégration d’Azure Active Directory à Lynda.com | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lynda.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: 8b6d9e52063fe83853b50fc21e7db2461facc841
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825440"
---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Didacticiel : Intégration d’Azure Active Directory à Lynda.com

Dans ce didacticiel, vous allez apprendre à intégrer Lynda.com à Azure Active Directory (Azure AD).

Intégrer Lynda.com à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l’accès à Lynda.com
- Vous pouvez autoriser les utilisateurs à être automatiquement connectés à Lynda.com (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Lynda.com, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Lynda.com pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Lynda.com à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-lyndacom-from-the-gallery"></a>Ajouter Lynda.com à partir de la galerie
Pour configurer l’intégration de Lynda.com à Azure AD, vous devez ajouter Lynda.com à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Lynda.com à partir de la galerie, réalisez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Lynda.com**.

    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/tutorial_lynda.com_search.png)

1. Dans le panneau de résultats, sélectionnez **Lynda.com**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/tutorial_lynda.com_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Lynda.com grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Lynda.com équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Lynda.com associé.

Pour cela, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** dans Lynda.com.

Pour configurer et tester l’authentification unique Azure AD avec Lynda.com, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Lynda.com](#creating-a-lyndacom-test-user)** pour avoir un équivalent de Britta Simon dans Lynda.com qui est lié à la représentation d’un utilisateur Azure AD.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Lynda.com.

**Pour configurer l’authentification unique Azure AD avec Lynda.com, réalisez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Lynda.com**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/lynda-tutorial/tutorial_lynda.com_samlbase.png)

1. Dans la section **Domaine et URL Lynda.com**, réalisez les étapes suivantes :

    ![Configurer l'authentification unique](./media/lynda-tutorial/tutorial_lynda.com_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.lynda.com/Shibboleth.sso/InCommon?providerId=<url>&target=<url> `

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir ces valeurs, contactez l’[équipe de support technique Lynda.com](https://www.linkedin.com/help/lynda/ask). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configure Single Sign-On](./media/lynda-tutorial/tutorial_lynda.com_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/lynda-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique du côté **Lynda.com**, vous devez envoyer le **XML des métadonnées** téléchargé à l’[équipe de support Lynda.com](https://www.linkedin.com/help/lynda/ask).

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/lynda-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-lyndacom-test-user"></a>Créer un utilisateur de test Lynda.com

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lynda.com.  
Lorsqu'un utilisateur assigné tente de se connecter à Lynda.com à l'aide du panneau d'accès, Lynda.com vérifie si cet utilisateur existe.  

Si aucun compte d'utilisateur n’est disponible, Lynda.com le crée automatiquement.

>[!NOTE]
>Vous pouvez utiliser n'importe quel outil ou API de création de compte utilisateur, fourni par Lynda.com, pour approvisionner des comptes utilisateur AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lynda.com.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à Lynda.com, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Lynda.com**.

    ![Configurer l'authentification unique](./media/lynda-tutorial/tutorial_lynda.com_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez  [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/lynda-tutorial/tutorial_general_01.png
[2]: ./media/lynda-tutorial/tutorial_general_02.png
[3]: ./media/lynda-tutorial/tutorial_general_03.png
[4]: ./media/lynda-tutorial/tutorial_general_04.png

[100]: ./media/lynda-tutorial/tutorial_general_100.png

[200]: ./media/lynda-tutorial/tutorial_general_200.png
[201]: ./media/lynda-tutorial/tutorial_general_201.png
[202]: ./media/lynda-tutorial/tutorial_general_202.png
[203]: ./media/lynda-tutorial/tutorial_general_203.png

