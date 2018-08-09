---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zendesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 9b467fa966c2a785677f47faaa4bb8bd3ed238e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427599"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Didacticiel : Intégration d’Azure Active Directory à Zendesk

Dans ce didacticiel, vous allez apprendre à intégrer Zendesk à Azure Active Directory (Azure AD).

L’intégration de Zendesk à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zendesk.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Zendesk (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zendesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique à Zendesk est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zendesk depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zendesk-from-the-gallery"></a>Ajout de Zendesk depuis la galerie
Pour configurer l’intégration de Zendesk à Azure AD, vous devez ajouter Zendesk, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zendesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Zendesk**, sélectionnez **Zendesk** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zendesk dans la liste des résultats](./media/zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zendesk, sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zendesk correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur dans Zendesk associé doit être établie.

Dans Zendesk, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Zendesk, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Zendesk](#create-a-zendesk-test-user)** : pour avoir un équivalent de Britta Simon dans Zendesk lié à la représentation de l’utilisateur Azure AD.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Zendesk.

**Pour configurer l’authentification unique Azure AD avec Zendesk, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Zendesk**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/zendesk-tutorial/tutorial_zendesk_samlbase.png)

1. Dans la section **Domaine et URL Zendesk**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Zendesk](./media/zendesk-tutorial/tutorial_zendesk_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.zendesk.com`

    b. Dans la zone de texte **Identificateur**, tapez la valeur au format suivant : `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise).

1. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Lien Téléchargement de certificat](./media/zendesk-tutorial/tutorial_zendesk_certificate.png)

1. Zendesk attend les assertions SAML dans un format spécifique. Il n’y a aucun attribut SAML obligatoire, mais si vous le souhaitez, vous pouvez ajouter un attribut à partir de la section **Attributs utilisateur** en suivant les étapes ci-dessous : 

     ![Configure Single Sign-On](./media/zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique add](./media/zendesk-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique addattb](./media/zendesk-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

    > [!NOTE]
    > Utilisez des attributs d’extension pour ajouter des attributs qui ne sont pas dans Azure AD par défaut. Cliquez sur [les attributs d’utilisateur qui peuvent être définis dans SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) pour obtenir la liste complète des attributs SAML que **Zendesk** accepte.

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/zendesk-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Zendesk**, cliquez sur **Configurer Zendesk** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Zendesk](./media/zendesk-tutorial/tutorial_zendesk_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zendesk en tant qu’administrateur.

1. Cliquez sur **Admin**.

1. Dans le volet de navigation gauche, cliquez sur **Settings**, puis sur **Security**.

1. Sur la page **Sécurité**, procédez comme suit : 

     ![Sécurité](./media/zendesk-tutorial/ic773089.png "Sécurité")

    ![Authentification unique](./media/zendesk-tutorial/ic773090.png "Authentification unique")

     a. Cliquez sur l’onglet **Administrateurs et Agents**.

     b. Sélectionnez **Single sign-on (SSO) and SAML**, puis **SAML**.

     c. Dans la zone de texte **URL SAML SSO**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure. 

     d. Dans la zone de texte **URL de déconnexion à distance**, collez la valeur de **l’URL de déconnexion** que vous avez copiée sur le portail Azure.

     e. Dans la zone de texte **Empreinte du certificat**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

     f. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/zendesk-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/zendesk-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/zendesk-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/zendesk-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-zendesk-test-user"></a>Créer un utilisateur de test Zendesk

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Zendesk. Zendesk prend en charge l’approvisionnement automatique d’utilisateurs, qui est activé par défaut. Des informations supplémentaires sur la configuration de l’approvisionnement automatique d’utilisateurs sont disponibles [ici](zendesk-provisioning-tutorial.md).

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

> [!NOTE]
> Les comptes de **l’utilisateur final** sont automatiquement approvisionnés lors de la connexion. Les comptes **Agent** et **Admin** doivent être approvisionnés manuellement dans **Zendesk** avant de vous connecter.

1. Connectez-vous à votre client **Zendesk** .

1. Sélectionnez l’onglet **Customer List** .

1. Sélectionnez l’onglet **User**, puis cliquez sur **Add**.

    ![Ajouter un utilisateur](./media/zendesk-tutorial/ic773632.png "Ajouter un utilisateur")
1. Tapez le **Nom** et **l’e-mail** d’un compte Azure AD que vous souhaitez approvisionner, puis cliquez sur **Save**.

    ![Nouvel utilisateur](./media/zendesk-tutorial/ic773633.png "Nouvel utilisateur")

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zendesk pour approvisionner des comptes d’utilisateurs Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zendesk.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Zendesk, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

1. Dans la liste des applications, sélectionnez **Zendesk**.

    ![Lien Zendesk dans la liste des applications](./media/zendesk-tutorial/tutorial_zendesk_app.png)

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Zendesk dans le volet d’accès, vous devez être connecté automatiquement à votre application Zendesk.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](zendesk-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
[4]: ./media/zendesk-tutorial/tutorial_general_04.png

[100]: ./media/zendesk-tutorial/tutorial_general_100.png

[200]: ./media/zendesk-tutorial/tutorial_general_200.png
[201]: ./media/zendesk-tutorial/tutorial_general_201.png
[202]: ./media/zendesk-tutorial/tutorial_general_202.png
[203]: ./media/zendesk-tutorial/tutorial_general_203.png
