---
title: 'Tutoriel : Intégration d’Azure Active Directory à XaitPorter | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd4f9e561a55c010273b48fb4232348baad3582
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200936"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Tutoriel : Intégration d’Azure Active Directory à XaitPorter

Dans ce didacticiel, vous allez apprendre à intégrer XaitPorter dans Azure Active Directory (Azure AD).

L’intégration de XaitPorter dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à XaitPorter.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à XaitPorter (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de Azure AD à XaitPorter, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement XaitPorter pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de XaitPorter depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-xaitporter-from-the-gallery"></a>Ajout de XaitPorter depuis la galerie
Pour configurer l’intégration de XaitPorter avec Azure AD, vous devez ajouter XaitPorter, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter XaitPorter à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **XaitPorter**, sélectionnez **XaitPorter** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![XaitPorter dans la liste des résultats](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec XaitPorter avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur XaitPorter équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur XaitPorter associé doit être établie.

Dans XaitPorter, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec XaitPorter, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer utilisateur de test XaitPorter](#create-a-xaitporter-test-user)** pour avoir un équivalent de Britta Simon dans XaitPorter lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application XaitPorter.

**Pour configurer l'authentification unique Azure AD avec XaitPorter, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **XaitPorter**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. Dans la section **Domaine et URL XaitPorter**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL XaitPorter](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.xaitporter.com/saml/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique XaitPorter](https://www.xait.com/support/).
     
1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes. 

    ![Lien Téléchargement de certificat](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Fournissez **l’adresse IP** ou **l’URL des métadonnées de fédération de l’application** à [l’équipe de support SmartRecruiters ](https://www.smartrecruiters.com/about-us/contact-us/), de sorte que XaitPorter puisse assurer l’accessibilité de l’adresse IP à partir de votre instance XaitPorter, en établissant une liste verte de leur côté. 

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise XaitPorter en tant qu’administrateur.

1. Cliquez sur le titre **Admin**.

    ![Configurer l'authentification unique](./media/xaitporter-tutorial/user1.png)

1. Sélectionnez **Gérer l’authentification unique** à partir de la liste déroulante **Configuration système**.

    ![Configurer l'authentification unique](./media/xaitporter-tutorial/user2.png)

1. Dans la section **GÉRER L’AUTHENTIFICATION UNIQUE**, procédez comme suit :

    ![Configurer l'authentification unique](./media/xaitporter-tutorial/user3.png)

    a. Sélectionnez **Activer l'authentification unique**.

    b. Dans la zone de texte **Paramètres de fournisseur d’identité**, collez **l’URL de métadonnées de fédération de l’application** que vous avez copiée dans le portail Azure et cliquez sur **Récupérer**.

    c. Sélectionnez **Activer la création automatique d’utilisateurs**.

    d. Cliquez sur **OK**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/xaitporter-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-xaitporter-test-user"></a>Créer un utilisateur de test XaitPorter

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans XaitPorter. Collaborez avec l’ [équipe du support technique XaitPorter](https://www.xait.com/support/)  pour ajouter des utilisateurs à la plateforme XaitPorter. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à XaitPorter.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à XaitPorter, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **XaitPorter**.

    ![Lien XaitPorter dans la liste des applications](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque XaitPorter dans le volet d’accès, vous devez être connecté automatiquement à votre application XaitPorter.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

