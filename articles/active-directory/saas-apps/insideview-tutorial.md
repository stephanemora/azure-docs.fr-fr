---
title: 'Didacticiel : Intégration d’Azure Active Directory à InsideView | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845ab85ae10a9d57bf3e263d49532675f60cd84f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517842"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Didacticiel : Intégration d’Azure Active Directory à InsideView

Dans ce didacticiel, vous allez apprendre à intégrer InsideView à Azure Active Directory (Azure AD).

L’intégration d’InsideView à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez déterminer qui a accès à InsideView.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à InsideView (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec InsideView, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un InsideView l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’InsideView à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-insideview-from-the-gallery"></a>Ajout d’InsideView à partir de la galerie
Pour configurer l’intégration d’InsideView à Azure AD, vous devez ajouter InsideView à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter InsideView à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **InsideView**.

    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

1. Dans le panneau des résultats, sélectionnez **InsideView**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec InsideView, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur InsideView équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur InsideView associé.

Dans InsideView, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec InsideView, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test InsideView](#creating-an-insideview-test-user)**  - pour avoir un équivalent de Britta Simon dans InsideView lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application InsideView.

**Pour configurer l’authentification unique Azure AD avec InsideView, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **InsideView**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

1. Dans la section **Domaine et URL InsideView**, procédez comme suit :

    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Contact [l’équipe de support InsideView](mailto:support@insideview.com) pour obtenir cette valeur.
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration d’InsideView**, cliquez sur **Configurer InsideView** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_insideview_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise InsideView en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **Admin**, **SingleSignOn Settings**, puis sur **Add SAML**.
   
   ![SAML Single Sign On Settings](./media/insideview-tutorial/ic794135.png "SAML Single Sign On Settings")

1. Dans la section **Add a New SAML** , procédez comme suit :

    ![Add a New SAML](./media/insideview-tutorial/ic794136.png "Add a New SAML")
   
    a. Dans la zone de texte **STS Name** , attribuez un nom à votre configuration.

    b. Dans la zone de texte  **Point de terminaison non sollicité SamIP/WS-Fed**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.
    
    c. Ouvrez votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat STS**.

    d. Dans la zone de texte **Mappage de l’ID d’utilisateur Crm**, saisissez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. Dans la zone de texte **Mappage d’e-mail Crm**, saisissez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Dans la zone de texte **Mappage du prénom Crm**, saisissez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. Dans la zone de texte **Mappage du nom Crm**, saisissez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-insideview-test-user"></a>Création d’un utilisateur de test InsideView

Pour permettre aux utilisateurs Azure AD de se connecter à InsideView, vous devez les configurer dans InsideView. Dans le cas d’InsideView, l’approvisionnement est une tâche manuelle.

Pour obtenir les utilisateurs ou les contacts créés dans InsideView, contactez l’[équipe de support InsideView](mailto:support@insideview.com).

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par InsideView, pour approvisionner des comptes utilisateur AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à InsideView.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à InsideView, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **InsideView**.

    ![Configurer l'authentification unique](./media/insideview-tutorial/tutorial_insideview_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette InsideView dans le panneau d’accès, vous êtes automatiquement connecté à votre application InsideView.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png
