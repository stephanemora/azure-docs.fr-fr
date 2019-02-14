---
title: "Tutoriel : Intégration d'Azure Active Directory à LogicMonitor | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3b6476fcedf849fb3305517ecfdcf29d07f545c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181539"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutoriel : Intégration d'Azure Active Directory à LogicMonitor

Dans ce didacticiel, vous allez apprendre à intégrer LogicMonitor à Azure Active Directory (Azure AD).

L’intégration de LogicMonitor à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LogicMonitor.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à LogicMonitor (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à LogicMonitor, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LogicMonitor pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LogicMonitor à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-logicmonitor-from-the-gallery"></a>Ajout de LogicMonitor à partir de la galerie
Pour configurer l’intégration de LogicMonitor à Azure AD, vous devez ajouter LogicMonitor, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter LogicMonitor à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **LogicMonitor**.

    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_search.png)

1. Dans le volet de résultats, sélectionnez **LogicMonitor**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LogicMonitor, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LogicMonitor équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur LogicMonitor associé doit être établie.

Dans LogicMonitor, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec LogicMonitor, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test LogicMonitor](#creating-a-logicmonitor-test-user)** pour avoir un équivalent de Britta Simon dans LogicMonitor lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LogicMonitor.

**Pour configurer l’authentification unique Azure AD avec LogicMonitor, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **LogicMonitor**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

1. Dans la section **Domaine et URL LogicMonitor**, procédez comme suit :

    ![Configurer l'authentification unique](./media/logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.logicmonitor.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support client LogicMonitor](https://www.logicmonitor.com/contact/). 
 


1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/logicmonitor-tutorial/tutorial_general_400.png)

1. Connectez-vous au site d’entreprise **LogicMonitor** en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Settings**.
   
    ![Paramètres](./media/logicmonitor-tutorial/ic790052.png "Paramètres")

1. Dans la barre de navigation située à gauche, cliquez sur **Single Sign On**
   
    ![Authentification unique](./media/logicmonitor-tutorial/ic790053.png "Authentification unique")

1. Dans la section **Single Sign-On SSO settings** , procédez comme suit :
   
    ![Paramètres d’authentification unique](./media/logicmonitor-tutorial/ic790054.png "paramètres d’authentification unique")
   
    a. Sélectionnez **Enable Single Sign-On**.

    b. Pour **Default Role Assignment**, sélectionnez **readonly**.
   
    c. Ouvrez le fichier de métadonnées téléchargé dans le Bloc-notes, puis collez le contenu dans la zone de texte **Identity Provider Metadata** .
   
    d. Cliquez sur **Enregistrer les modifications**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/logicmonitor-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Création d’un utilisateur de test de LogicMonitor

Pour que les utilisateurs d’Azure AD puissent se connecter, ils doivent être approvisionnés dans l’application LogicMonitor à l’aide de leurs noms d’utilisateur Azure Active Directory.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous au site d’entreprise LogicMonitor en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Settings**, puis sur **Roles and Users**.
   
    ![Rôles et utilisateurs](./media/logicmonitor-tutorial/ic790056.png "Rôles et utilisateurs")

1. Cliquez sur **Add**.

1. Dans la section **Add an account** , procédez comme suit :
   
    ![Ajouter un compte](./media/logicmonitor-tutorial/ic790057.png "Ajouter un compte")
   
    a. Entrez les valeurs appropriées dans les champs **Nom d’utilisateur**, **Adresse de messagerie**, **Mot de passe** et **Confirmer le mot de passe** pour l’utilisateur Azure Active Directory.
   
    b. Sélectionnez **Rôles**, **Afficher les autorisations** et **État**.
   
    c. Cliquez sur **Envoyer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par LogicMonitor, pour approvisionner des comptes utilisateur Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LogicMonitor.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LogicMonitor, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **LogicMonitor**.

    ![Configurer l'authentification unique](./media/logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la vignette LogicMonitor dans le volet d’accès, vous devez être connecté automatiquement à votre application LogicMonitor.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/logicmonitor-tutorial/tutorial_general_203.png

