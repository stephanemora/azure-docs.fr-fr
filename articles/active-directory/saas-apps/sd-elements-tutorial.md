---
title: "Didacticiel : Intégration d'Azure Active Directory à SD Elements | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 0081946ff62e42e1fb601b135c7102dd83e84fe3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158908"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutoriel : Intégration d'Azure Active Directory à SD Elements

Dans ce didacticiel, vous allez apprendre à intégrer SD Elements avec Azure Active Directory (Azure AD).

L’intégration de SD Elements à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SD Elements.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SD Elements (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SD Elements, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SD Elements pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SD Elements à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sd-elements-from-the-gallery"></a>Ajout de SD Elements à partir de la galerie
Pour configurer l’intégration de SD Elements avec Azure AD, vous devez ajouter SD Elements disponible dans la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SD Elements à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **SD Elements**.

    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_search.png)

1. Dans le panneau de résultats, sélectionnez **SD Elements**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SD Elements sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SD Elements équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur SD Elements associé doit être établie.

Dans SD Elements, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec SD Elements, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d'un utilisateur de test SD Elements](#creating-a-sd-elements-test-user)** pour avoir un équivalent de Britta Simon dans SD Elements lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SD Elements.

**Pour configurer l’authentification unique Azure AD avec SD Elements, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **SD Elements**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sdelements_samlbase.png)

1. Dans la section **Domaine et URL SD Elements**, procédez comme suit :

    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sdelements_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SD Elements](mailto:support@sdelements.com).

1. L’application SD Elements attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs utilisateur** de l’application. La capture d’écran suivante montre un exemple :

    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sdelements_attribute.png)

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit : 

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/sd-elements-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/sd-elements-tutorial/tutorial_officespace_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Cliquez sur **OK**.
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/sd-elements-tutorial/tutorial_sdelements_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de SD Elements** , cliquez sur **Configurer SD Elements** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sdelements_configure.png)

1. Pour activer l'authentification unique, contactez votre [équipe de support technique SD Elements](mailto:support@sdelements.com) et fournissez-leur le fichier de certificat téléchargé. 

1. Dans une autre fenêtre de navigateur, authentifiez-vous auprès de votre client SD Elements en tant qu’administrateur.

1. Dans le menu du haut, cliquez sur **Système**, puis sur **Authentification unique**. 
   
    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sd-elements_09.png) 

1. Dans la boîte de dialogue **Paramètres d’authentification unique** , procédez comme suit :
   
    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Pour **SSO Type**, sélectionnez **SAML**.
   
    b. Dans la zone de texte **Identity Provider Entity ID** (ID d’entité du fournisseur d'identité), collez la valeur de l’**ID d’entité SAML** copiée à partir du portail Azure. 
   
    c. Dans la zone de texte **Identity Provider Single Sign-On Service** (Service d’authentification unique du fournisseur d’identité), collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure. 
   
    d. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/sd-elements-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-sd-elements-test-user"></a>Création d'un utilisateur de test SD Elements

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SD Elements. Dans le cas de SD Elements, la création d'utilisateurs SD Elements est une tâche manuelle.

**Pour créer un utilisateur appelé Britta Simon dans SD Elements, procédez comme suit :**

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise SD Elements en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Gestion des utilisateurs**, puis sur **Utilisateurs**.
   
    ![Création d'un utilisateur de test SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Cliquez sur **Add New User**.
   
    ![Création d'un utilisateur de test SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)
 
1. Dans la boîte de dialogue **Ajouter un nouvel utilisateur**, procédez comme suit :
   
    ![Création d'un utilisateur de test SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.
   
    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.
   
    c. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.
   
    d. Pour **Role**, sélectionnez **User**. 
   
    e. Cliquez sur **Créer l’utilisateur**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SD Elements.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SD Elements, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **SD Elements**.

    ![Configurer l'authentification unique](./media/sd-elements-tutorial/tutorial_sdelements_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
  
Lorsque vous cliquez sur la mosaïque SD Elements dans le volet d’accès, vous devez être connecté automatiquement à votre application SD Elements.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/sd-elements-tutorial/tutorial_general_203.png

