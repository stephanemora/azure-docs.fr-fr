---
title: 'Didacticiel : Intégration d’Azure Active Directory à O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57838d5f5a49045138ce9adbdcf7855aeab783e8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172113"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Tutoriel : Intégration d’Azure Active Directory à O.C. Tanner - AppreciateHub.

Dans ce didacticiel, vous allez apprendre à intégrer O.C. Tanner - AppreciateHub à Azure Active Directory (Azure AD).

L’intégration d’O.C. Tanner - AppreciateHub à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à O.C. Tanner - AppreciateHub.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à O.C. Tanner - AppreciateHub (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à O.C. Tanner - AppreciateHub, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement O.C. Un abonnement Tanner - AppreciateHub pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’O.C. Tanner - AppreciateHub à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Ajout d’O.C. Tanner - AppreciateHub à partir de la galerie
Pour configurer l’intégration d’O.C. Tanner - AppreciateHub à Azure AD, vous devez ajouter O.C. Tanner - AppreciateHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter O.C. Tanner - AppreciateHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **O.C. Tanner - AppreciateHub**.

    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. Dans le panneau des résultats, sélectionnez **O.C. Tanner - AppreciateHub**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur O.C. Tanner - AppreciateHub équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur O.C. Tanner - AppreciateHub associé doit être établie.

Dans O.C. Tanner - AppreciateHub, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test O.C. Tanner - AppreciateHub](#creating-a-oc-tanner---appreciatehub-test-user)** pour avoir un équivalent de Britta Simon dans O.C. Tanner - AppreciateHub lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application O.C. Tanner - AppreciateHub.

**Pour configurer l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **O.C. Tanner - AppreciateHub** cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. Dans la section **Domaine et URL O.C. Tanner - AppreciateHub**, procédez comme suit :

    ![Configure Single Sign-On](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique O.C. Tanner - AppreciateHub](mailto:sso@octanner.com).

    b. Ouvrez le fichier de métadonnées à l’aide du lien suivant : [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Recherchez le nœud **md:AssertionConsumerService** . 
   
    d. Copiez la valeur de l’attribut **Location** . 
   
    ![Configurer les paramètres d’application][12]
   
    e. Dans la zone de texte **URL de connexion** , collez la valeur que vous avez obtenue à l’étape précédente.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique côté **O.C. Tanner - AppreciateHub**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe du support technique O.C. Tanner - AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Création d’un utilisateur de test O.C. Tanner - AppreciateHub

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans O.C. Tanner - AppreciateHub.

**Pour créer un utilisateur nommé Britta Simon dans O.C. Tanner - AppreciateHub, procédez comme suit :**

Demandez à [l’équipe du support technique O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) de créer un utilisateur dont l’attribut nameID est identique au nom d’utilisateur de Britta Simon dans Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à O.C. Tanner - AppreciateHub.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à O.C. Tanner - AppreciateHub, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **O.C. Tanner - AppreciateHub**.

    ![Configure Single Sign-On](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Quand vous cliquez sur la vignette O.C. Tanner - AppreciateHub dans le volet d’accès, vous devez être connecté automatiquement à votre application O.C. Tanner - AppreciateHub.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

