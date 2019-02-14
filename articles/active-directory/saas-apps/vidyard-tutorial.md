---
title: 'Tutoriel : Intégration d’Azure Active Directory à Vidyard | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d25520f88869025f7e262d81295ee64f8e12d21
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163592"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Tutoriel : Intégration d’Azure AD à Vidyard

Dans ce didacticiel, vous allez apprendre à intégrer Vidyard à Azure Active Directory (Azure AD).

L’intégration de Vidyard à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Vidyard.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Vidyard (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Vidyard, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique Vidyard est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Vidyard depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-vidyard-from-the-gallery"></a>Ajout de Vidyard depuis la galerie
Pour configurer l’intégration de Vidyard avec Azure AD, vous devez ajouter Vidyard à votre liste d’applications SaaS gérées depuis la galerie.

**Pour ajouter Vidyard depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Vidyard**, sélectionnez **Vidyard** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Vidyard dans la liste des résultats](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Vidyard avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Vidyard équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Vidyard associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec Vidyard, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Vidyard](#create-a-vidyard-test-user)** pour avoir un équivalent de Britta Simon dans Vidyard lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Vidyard.

**Pour configurer l'authentification unique Azure AD avec Vidyard, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Vidyard**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Dans la section **Domaines et URL Vidyard**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domain et URL Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domain et URL Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous mettrez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/vidyard-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Vidyard**, cliquez sur **Configurer Vidyard** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Vidyard Software en tant qu’administrateur.

1. Dans le tableau de bord Vidyard, sélectionnez **Groupe** > **Sécurité**

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure1.png)

1. Cliquez sur l’onglet **Nouveau profil**.

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure2.png)

1. Dans la section **Configuration de SAML**, procédez comme suit :

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Entrez le nom du profil général dans la zone de texte **Nom du profil**.

    b. Copiez la valeur **SSO User Login Page** (Page de connexion utilisateur SSO), collez-la dans la zone de texte **URL de connexion** dans la section **Domaine et URL Vidyard** dans le portail Azure.

    c. Copiez la valeur **ACS URL** (URL ACS), collez-la dans la zone de texte **URL de réponse** dans la section **Domaine et URL Vidyard** dans le portail Azure.

    d. Copiez la valeur **Issuer/Metadata URL** (URL de l’émetteur/métadonnées), collez-la dans la zone de texte **Identificateur** dans la section **Domaine et URL Vidyard** dans le portail Azure.

    e. Dans le Bloc-notes, ouvrez le fichier de certificat que vous avez téléchargé sur le portail Azure, copiez son contenu, puis collez-le dans la zone de texte **Certificat X.509**.

    f. Dans la zone de texte **URL du point de terminaison SAML**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    g. Cliquez sur **Confirmer**.

1. Dans l’onglet Authentification unique, sélectionnez **Attribuer** en regard d’un profil existant

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Une fois le profil SSO créé, attribuez-le aux groupes auxquels les utilisateurs devront accéder via Azure. Si un utilisateur n’existe pas dans le groupe auquel il est attribué, Vidyard créer automatiquement un compte d’utilisateur et attribue le rôle en temps réel.

1. Sélectionnez le groupe de votre organisation, qui est visible dans **Groups Available to Assign** (Groupes disponibles pour l’attribution).

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure5.png)

1. Vous pouvez voir les groupes attribués sous **Groups Currently Assigned** (Groupes actuellement attribués). Sélectionnez un rôle pour le groupe de votre organisation et cliquez sur **Confirmer**.

    ![Configuration de Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Pour plus d’informations, consultez [ce document](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/vidyard-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/vidyard-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/vidyard-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-vidyard-test-user"></a>Création d’un utilisateur de test Vidyard

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Vidyard. Vidyard prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Vidyard s’il n’existe pas déjà.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez  [l’équipe du support technique de Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Vidyard.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Vidyard, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Vidyard**.

    ![Lien Vidyard dans la liste des applications](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Vidyard dans le volet d’accès, vous devez être connecté automatiquement à votre application Vidyard.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

