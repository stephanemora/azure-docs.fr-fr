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
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 794039ee1a5b1cf3b382e0f0769383b1e033e982
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046937"
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
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Ajout de Jamf Pro à partir de la galerie
Pour configurer l’intégration de Jamf Pro à Azure AD, vous devez ajouter Jamf Pro à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Jamf Pro à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Jamf Pro**, sélectionnez **Jamf Pro** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Jamf Pro dans la liste des résultats](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jamf Pro avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jamf Pro équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Jamf Pro associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Jamf Pro, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Jamf Pro](#create-a-jamf-pro-test-user)** pour avoir dans Jamf Pro un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Jamf Pro.

**Pour configurer l'authentification unique Azure AD avec Jamf Pro, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Jamf Pro**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Dans la section **Domaines et URL Jamf Pro**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez la valeur avec l’identificateur réel à partir de la section **Authentification unique** dans le portail Jamf Pro. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez extraire la valeur de **sous-domaine** réelle à partir de la valeur d’identificateur et utiliser ces informations de **sous-domaine** dans l’URL de connexion et l’URL de réponse.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

8. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

9. Cliquez sur **Authentification unique**.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

10. Faites défiler jusqu’à **Fournisseur d’identité** sous la section **Single Sign-On** (Authentification unique) et effectuez les étapes suivantes :

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Sélectionnez **Autres** comme option dans le menu déroulant **Fournisseur d’identité**.

    b. Dans la zone de texte **OTHER PROVIDER (Autre fournisseur)**, entrez **Azure AD**.

    c. Sélectionnez **Metadata URL** (URL des métadonnées) comme option dans la liste déroulante **IDENTITY PROVIDER METADATA SOURCE** (Source des métadonnées du fournisseur d’identité), puis dans la zone de texte suivante, collez la valeur d’**URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

    d. Copiez la valeur de l’**ID d’entité** et collez-la dans la zone de texte **Identificateur (ID d’entité)**  de la section **Domaine et URL Jamf Pro** du portail Azure.

    >[!NOTE]
    > Ici, `aadsso` est la partie sous-domaine (indiquée à des fins de référence). Utilisez cette valeur pour remplir l’URL de connexion et l’URL de réponse dans la section **URL et domaine Pro Jamf** sur le portail Azure.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-jamf-pro-test-user"></a>Créer un utilisateur de test Jamf Pro

Pour se connecter à Jamf Pro, les utilisateurs d’Azure AD doivent être provisionnés dans Jamf Pro. Dans le cas de Jamf Pro, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

2. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Cliquez sur **Jamf Pro User Accounts & Groups (Groupes et comptes d’utilisateur Jamf Pro)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user1.png)

4. Cliquez sur **Nouveau**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user2.png)

5. Sélectionnez **Create Standard Account (Créer un compte standard)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user3.png)

6. Dans la boîte de dialogue **New Account (Nouveau compte)**, procédez comme suit :

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

2. Dans la liste des applications, sélectionnez **Jamf Pro**.

    ![Lien Jamf Pro dans la liste des applications](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
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

