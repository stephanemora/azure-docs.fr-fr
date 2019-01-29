---
title: "Tutoriel : Intégration d'Azure Active Directory à Sugar CRM | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 1aa483f0333cd609ab599199415e12f844d722ea
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819422"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Didacticiel : Intégration d'Azure AD à Sugar CRM

Dans ce didacticiel, vous allez apprendre à intégrer Sugar CRM avec Azure Active Directory (Azure AD).

L’intégration de Sugar CRM dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Sugar CRM.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Sugar CRM (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sugar CRM, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Sugar CRM pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Sugar CRM à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sugar-crm-from-the-gallery"></a>Ajout de Sugar CRM à partir de la galerie
Pour configurer l’intégration de Sugar CRM à Azure AD, vous devez ajouter Sugar CRM à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Sugar CRM à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Sugar CRM**.

    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. Dans le volet de résultats, sélectionnez **Sugar CRM**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Sugar CRM avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Sugar CRM correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Sugar CRM associé doit être établie.

Dans Sugar CRM, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Sugar CRM, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Sugar CRM](#creating-a-sugar-crm-test-user)** pour avoir un équivalent de Britta Simon dans Sugar CRM, lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Sugar CRM.

**Pour configurer l’authentification unique Azure AD avec Sugar CRM, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Sugar CRM**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. Dans la section **Domaine et URL Sugar CRM**, procédez comme suit :

    ![Configurer l'authentification unique](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Sugar CRM](https://support.sugarcrm.com/). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Sugar CRM**, cliquez sur **Configurer Sugar CRM** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l'authentification unique](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sugar CRM en tant qu’administrateur.

1. Accédez à **Admin**.
   
    ![Administrateur](./media/sugarcrm-tutorial/ic795888.png "Administrateur")

1. Dans la section **Administration**, cliquez sur **Password Management (Gestion des mots de passe)**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. Sélectionnez **Enable SAML Authentication**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. Dans la section **SAML Authentication** , procédez comme suit :
   
    ![Authentification SAML](./media/sugarcrm-tutorial/ic795891.png "Authentification SAML")  
 
    a. Dans la zone de texte **URL de connexion**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
  
    b. Dans la zone de texte **URL de déconnexion unique**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.
  
    c. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
  
    d. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Création d’un utilisateur de test Sugar CRM

Pour se connecter à Sugar CRM, les utilisateurs d’Azure AD doivent être approvisionnés dans Sugar CRM.

Dans le cas de Sugar CRM, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Sugar CRM** en tant qu’administrateur.

1. Accédez à **Admin**.
   
    ![Administrateur](./media/sugarcrm-tutorial/ic795888.png "Administrateur")

1. Dans la section **Administration**, cliquez sur **User Management (Gestion des utilisateurs)**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. Accédez à **Users (Utilisateurs) \> Create New User (Créer un utilisateur)**.
   
    ![Créer un nouvel utilisateur](./media/sugarcrm-tutorial/ic795894.png "Créer un nouvel utilisateur")

1. Dans l’onglet **User Profile** , procédez comme suit :
   
    ![Nouvel utilisateur](./media/sugarcrm-tutorial/ic795895.png "Nouvel utilisateur")

    a. Indiquez le **prénom**, le **nom** et l’**adresse de messagerie** du compte Azure AD valide que vous souhaitez approvisionner, dans les zones de texte correspondantes.
  
1. Dans **Status**, sélectionnez **Active**.

1. Dans l’onglet Password, procédez comme suit :
   
    ![Nouvel utilisateur](./media/sugarcrm-tutorial/ic795896.png "Nouvel utilisateur")

    a. Tapez le mot de passe dans la zone de texte correspondante.

    b. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Sugar CRM, pour approvisionner des comptes d’utilisateur AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sugar CRM.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Sugar CRM, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Sugar CRM**.

    ![Configurer l'authentification unique](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Sugar CRM dans le volet d’accès, vous devez être connecté automatiquement à votre application Sugar CRM.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

