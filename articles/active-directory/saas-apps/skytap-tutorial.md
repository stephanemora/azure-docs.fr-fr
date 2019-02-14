---
title: "Tutoriel : Intégration d'Azure Active Directory à Skytap | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec6d34ec82d5239af653e69532703a931f9cb12a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182746"
---
# <a name="tutorial-azure-active-directory-integration-with-skytap"></a>Tutoriel : Intégration d'Azure Active Directory à Skytap

Dans ce tutoriel, vous allez apprendre à intégrer Skytap à Azure Active Directory (Azure AD).

L’intégration de Skytap dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Skytap.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Skytap (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Skytap, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Skytap pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Skytap à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-skytap-from-the-gallery"></a>Ajout de Skytap à partir de la galerie
Pour configurer l’intégration de Skytap avec Azure AD, vous devez ajouter Skytap, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Skytap à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Skytap**, sélectionnez **Skytap** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Skytap dans la liste des résultats](./media/skytap-tutorial/tutorial_skytap_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Skytap avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Skytap équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Skytap associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec Skytap, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Skytap](#create-a-skytap-test-user)** pour avoir un équivalent de Britta Simon dans Skytap qui soit associé à la représentation de l’utilisateur Azure AD.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Skytap.

**Pour configurer l’authentification unique Azure AD avec Skytap, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Skytap**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/skytap-tutorial/tutorial_skytap_samlbase.png)

1. Dans la section **Domaines et URL Skytap**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Skytap](./media/skytap-tutorial/tutorial_skytap_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `http://pingone.com/<custom EntityID>`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Skytap](./media/skytap-tutorial/tutorial_skytap_url1.png)

    c. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`
     
    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://pingone.com/1.0/<custom ID>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de connexion et l’état de relais réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de Skytap](mailto:support@skytap.com). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/skytap-tutorial/tutorial_skytap_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/skytap-tutorial/tutorial_general_400.png)
    
1. Pour configurer l’authentification unique côté **Skytap**, vous devez envoyer le **fichier XML de métadonnées** téléchargé à [l’équipe du support technique de Skytap](mailto:support@skytap.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/skytap-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/skytap-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/skytap-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/skytap-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-skytap-test-user"></a>Créer un utilisateur de test Skytap

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Skytap. Collaborez avec l' [équipe du support technique de Skytap](mailto:support@skytap.com) pour ajouter des utilisateurs à la plateforme Skytap. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Skytap.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Skytap, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Skytap**.

    ![Lien Skytap dans la liste des applications](./media/skytap-tutorial/tutorial_skytap_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Skytap dans le panneau d’accès, vous devez être connecté automatiquement à votre application Skytap.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skytap-tutorial/tutorial_general_01.png
[2]: ./media/skytap-tutorial/tutorial_general_02.png
[3]: ./media/skytap-tutorial/tutorial_general_03.png
[4]: ./media/skytap-tutorial/tutorial_general_04.png

[100]: ./media/skytap-tutorial/tutorial_general_100.png

[200]: ./media/skytap-tutorial/tutorial_general_200.png
[201]: ./media/skytap-tutorial/tutorial_general_201.png
[202]: ./media/skytap-tutorial/tutorial_general_202.png
[203]: ./media/skytap-tutorial/tutorial_general_203.png

