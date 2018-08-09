---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Jamf Pro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: 94b8b935728110cd5dd07b2066e8320274e3b082
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428415"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Tutoriel : Intégration d’Azure Active Directory à Jamf Pro

Dans ce tutoriel, vous allez apprendre à intégrer Jamf Pro dans Azure Active Directory (Azure AD).

L’intégration de Jamf Pro à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Jamf Pro.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Jamf Pro (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Jamf Pro, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Jamf Pro pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jamf Pro à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Ajout de Jamf Pro à partir de la galerie
Pour configurer l’intégration de Jamf Pro à Azure AD, vous devez ajouter Jamf Pro à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Jamf Pro à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Jamf Pro**, sélectionnez **Jamf Pro** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Jamf Pro dans la liste des résultats](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jamf Pro avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jamf Pro équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Jamf Pro associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Jamf Pro, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Jamf Pro](#create-a-jamf-pro-test-user)** pour avoir dans Jamf Pro un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Jamf Pro.

**Pour configurer l'authentification unique Azure AD avec Jamf Pro, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Jamf Pro**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

1. Dans la section **Domaines et URL Jamf Pro**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez la valeur avec l’identificateur réel à partir de la section **Authentification unique** dans le portail Jamf Pro. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez extraire la valeur de **sous-domaine** réelle à partir de la valeur d’identificateur et utiliser ces informations de **sous-domaine** dans l’URL de connexion et l’URL de réponse.

1. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

1. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Cliquez sur **Authentification unique**.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

1. Sur la page **Authentification unique**, effectuez les étapes suivantes :

    ![Authentification unique Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Sélectionnez **Serveur Jamf Pro** pour activer l’accès avec authentification unique.

    b. En sélectionnant **Autoriser le contournement pour tous les utilisateurs**, les utilisateurs ne seront pas redirigés vers la page de connexion du fournisseur d’identité pour l’authentification, mais pourront se connecter à Jamf Pro directement. Lorsqu’un utilisateur tente d’accéder à Jamf Pro via le fournisseur d’identité, l’autorisation et l’authentification SSO lancée par le fournisseur d’identité se produisent.

    c. Sélectionnez l’option **NameID** pour **MAPPAGE D’UTILISATEUR : SAML**. Par défaut, ce paramètre est défini sur **NameID**, mais vous pouvez définir un attribut personnalisé.

    d. Sélectionnez **E-mail** pour **MAPPAGE UTILISATEUR : JAMF PRO**. Jamf Pro mappe les attributs SAML envoyés par le fournisseur d’identité de différentes manières : par utilisateurs et par groupes. Lorsqu’un utilisateur tente d’accéder à Jamf Pro, Jamf Pro obtient par défaut des informations sur l’utilisateur de la part du fournisseur d’identité, et les compare avec les comptes d’utilisateur Jamf Pro. Si le compte d’utilisateur entrant n’existe pas dans Jamf Pro, alors une correspondance de nom du groupe se produit.

    e. Collez la valeur `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` dans la zone de texte **NOM DE L’ATTRIBUT DE GROUPE**.
 
1. Sur la même page, faites défiler jusqu’à **IDENTITY PROVIDER** (Fournisseur d’identité) sous la section **Authentification unique** et effectuez les étapes suivantes :

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Sélectionnez **Autres** comme option dans le menu déroulant **Fournisseur d’identité**.

    b. Dans la zone de texte **OTHER PROVIDER (Autre fournisseur)**, entrez **Azure AD**.

    c. Sélectionnez **Metadata URL** (URL des métadonnées) comme option dans la liste déroulante **IDENTITY PROVIDER METADATA SOURCE** (Source des métadonnées du fournisseur d’identité), puis dans la zone de texte suivante, collez la valeur d’**URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

    d. Copiez la valeur de l’**ID d’entité** et collez-la dans la zone de texte **Identificateur (ID d’entité)**  de la section **Domaine et URL Jamf Pro** du portail Azure.

    >[!NOTE]
    > Ici, la valeur floutée est la partie du sous-domaine. Utilisez cette valeur pour remplir l’URL de connexion et l’URL de réponse dans la section **Domaine et URL Jamf Pro** du portail Azure.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-jamf-pro-test-user"></a>Créer un utilisateur de test Jamf Pro

Pour se connecter à Jamf Pro, les utilisateurs d’Azure AD doivent être provisionnés dans Jamf Pro. Dans le cas de Jamf Pro, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

1. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Cliquez sur **Jamf Pro User Accounts & Groups (Groupes et comptes d’utilisateur Jamf Pro)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user1.png)

1. Cliquez sur **Nouveau**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user2.png)

1. Sélectionnez **Create Standard Account (Créer un compte standard)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user3.png)

1. Dans la boîte de dialogue **New Account (Nouveau compte)**, procédez comme suit :

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Dans la zone de texte **USERNAME (Nom d’utilisateur)**, tapez le nom complet BrittaSimon.

    b. Sélectionnez les options adaptées à votre organisation pour **ACCESS LEVEL** (Niveau d’accès), **PRIVILEGE SET (Ensemble de privilèges)** et **ACCESS STATUS (État de l’accès)**.
    
    c. Dans la zone de texte **FULL NAME (Nom complet)**, tapez Britta Simon.

    d. Dans la zone de texte **EMAIL ADDRESS (Adresse e-mail)**, tapez l’adresse e-mail du compte de Britta Simon.

    e. Dans la zone de texte **PASSWORD (Mot de passe)**, tapez un mot de passe pour l’utilisateur.

    f. Dans la zone de texte **VERIFY PASSWORD (Vérifier le mot de passe)**, tapez le mot de passe de l’utilisateur.

    g. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jamf Pro.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Jamf Pro, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Jamf Pro**.

    ![Lien Jamf Pro dans la liste des applications](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Jamf Pro dans le volet d’accès, vous devez être connecté automatiquement à votre application Jamf Pro.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

