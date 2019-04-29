---
title: "Didacticiel : Intégration d'Azure Active Directory à Workteam | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60523983"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Didacticiel : Intégration d'Azure Active Directory à Workteam

Dans ce didacticiel, vous allez apprendre à intégrer Workteam à Azure Active Directory (Azure AD).

L’intégration de Workteam dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Workteam.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workteam (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Workteam, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Workteam pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Workteam à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-workteam-from-the-gallery"></a>Ajout de Workteam à partir de la galerie
Pour configurer l’intégration de Workteam à Azure AD, vous devez ajouter Workteam à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workteam à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Workteam**, sélectionnez **Workteam** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Workteam dans la liste des résultats](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Workteam avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Workteam équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Workteam associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Workteam, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Workteam](#create-a-workteam-test-user)** pour avoir un équivalent de Britta Simon dans Workteam lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application Workteam.

**Pour configurer l’authentification unique Azure AD avec Workteam, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Workteam**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. Dans la section **Workteam Domain and URLs** (Domaine et URL Workteam), l’utilisateur n’aura à effectuer aucune étape dans la mesure où l’application est préalablement intégrée à Azure.

    ![Informations d’authentification unique dans la section Workteam Domain and URLs (Domaine et URL Workteam)](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Workteam Domain and URLs (Domaine et URL Workteam)](./media/workteam-tutorial/tutorial_workteam_url1.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL `https://app.workte.am`
     
5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/workteam-tutorial/tutorial_general_400.png)
    
7. Dans la section **Workteam Configuration** (Configuration de Workteam), cliquez sur **Configure Workteam** (Configurer Workteam) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configuration de Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à Workteam en tant qu’administrateur de la sécurité.

9. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Organization settings** (Paramètres de l’organisation). 

    ![Paramètres de Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. Dans la section **AUTHENTICATION** (Authentification), cliquez sur **l’icône Paramètres**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. Sur la page **SAML Settings** , procédez comme suit :

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Sous **SAML IdP** (Fournisseur d’identité SAML), sélectionnez **AD Azure**.

    b. Dans la zone de texte **SAML Single Sign-On Service URL** (URL du service d’authentification unique SAML), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du Portail Azure.

    c. Dans la zone de texte **SAML Entity ID** (ID d’entité SAML), collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du Portail Azure.

    d. Dans le Bloc-notes, ouvrez le **certificat codé en base 64** que vous avez téléchargé à partir du Portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat de signature SAML (Base64)** .

    e. Cliquez sur **OK**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/workteam-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/workteam-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/workteam-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/workteam-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-workteam-test-user"></a>Créer un utilisateur de test Workteam

Pour se connecter à Workteam, les utilisateurs d’Azure AD doivent être approvisionnés dans Workteam. Dans Workteam, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Workteam en tant qu’administrateur de la sécurité.

2. En haut et au milieu de la page **Organization settings** (Paramètres de l’organisation), cliquez sur **USERS** (Utilisateurs), puis cliquez sur **NEW USER** (Nouvel utilisateur).

    ![Utilisateur Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Dans la page **New employee** (Nouvel employé), effectuez les étapes suivantes :

    ![Nouvel utilisateur Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Dans la zone de texte **Name** (Nom), saisissez le prénom de l’utilisateur, par exemple **Brittasimon**.

    b. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    c. Cliquez sur **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workteam.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Workteam, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Workteam**.

    ![Lien Workteam dans la liste des applications](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Workteam dans le volet d’accès, vous devez être connecté automatiquement à votre application Workteam.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

