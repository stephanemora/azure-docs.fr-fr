---
title: 'Tutoriel : Intégration d’Azure Active Directory à Fuze | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fuze.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9780b4bf-1fd1-48c1-9ceb-f750225ae08a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 2fdad7df43310132684eae983eae2278629a9b63
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810191"
---
# <a name="tutorial-azure-active-directory-integration-with-fuze"></a>Didacticiel : Intégration d’Azure Active Directory avec Fuze

Dans ce didacticiel, vous allez apprendre à intégrer Fuze à Azure Active Directory (Azure AD).

L’intégration de Fuze dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Fuze
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Fuze (par authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Fuze, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Fuze pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Fuze depuis la galerie
1. Configuration et test de l’authentification unique Azure AD


## <a name="adding-fuze-from-the-gallery"></a>Ajout de Fuze depuis la galerie
Pour configurer l’intégration de Fuze avec Azure AD, vous devez ajouter Fuze à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Fuze à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Fuze**.

    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/tutorial_fuze_000.png)

1. Dans le volet de résultats, sélectionnez **Fuze**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/tutorial_fuze_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Fuze avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Fuze équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Fuze associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Fuze.

Pour configurer et tester l’authentification unique Azure AD avec Fuze, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Fuze](#creating-a-fuze-test-user)** pour avoir un équivalent de Britta Simon dans Fuze lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Fuze.

**Pour configurer l’authentification unique Azure AD avec Fuze, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Fuze**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/fuze-tutorial/tutorial_fuze_01.png)

1. Dans la section **Domaine et URL Fuze**, procédez comme suit :

    ![Configurer l'authentification unique](./media/fuze-tutorial/tutorial_fuze_020.png)
    
    Dans la zone de texte **URL de connexion**, tapez l’URL de connexion suivante : `https://www.thinkingphones.com/jetspeed/portal/`

1.  Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/fuze-tutorial/tutorial_general_400.png)

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier xml sur votre ordinateur.

    ![Configurer l'authentification unique](./media/fuze-tutorial/tutorial_fuze_05.png) 

1. Pour configurer l’authentification unique du côté **Fuze**, vous devez envoyer le **XML de métadonnées** téléchargé à [l’équipe de support technique de Fuze](https://www.fuze.com/support). Ils effectueront les réglages nécessaires pour que la connexion SSO SAML soit définie correctement des deux côtés.


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/create_aaduser_01.png) 

1. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/create_aaduser_02.png) 

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/fuze-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**. 


### <a name="creating-a-fuze-test-user"></a>Création d’un utilisateur de test Fuze

L’application Fuze prend en charge la configuration juste-à-temps complète. Ainsi, les utilisateurs sont créés automatiquement lors de leur connexion. Pour toute autre précision, contactez le [support technique](https://www.fuze.com/support) de Fuze.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fuze.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Fuze, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires et à **Applications d’entreprise** puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Fuze**.

    ![Configurer l'authentification unique](./media/fuze-tutorial/tutorial_fuze_50.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Fuze dans le volet d’accès, vous devez être connecté automatiquement à votre application Fuze.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fuze-tutorial/tutorial_general_01.png
[2]: ./media/fuze-tutorial/tutorial_general_02.png
[3]: ./media/fuze-tutorial/tutorial_general_03.png
[4]: ./media/fuze-tutorial/tutorial_general_04.png

[100]: ./media/fuze-tutorial/tutorial_general_100.png

[200]: ./media/fuze-tutorial/tutorial_general_200.png
[201]: ./media/fuze-tutorial/tutorial_general_201.png
[202]: ./media/fuze-tutorial/tutorial_general_202.png
[203]: ./media/fuze-tutorial/tutorial_general_203.png