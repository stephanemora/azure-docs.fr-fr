---
title: "Didacticiel : Intégration d'Azure Active Directory à Workspot Control | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098662"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Didacticiel : Intégration d'Azure Active Directory à Workspot Control

Dans ce tutoriel, vous allez apprendre à intégrer Workspot Control à Azure Active Directory (Azure AD).

L’intégration de Workspot Control à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Workspot Control.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workspot Control (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Workspot Control, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Workspot Control pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Workspot Control depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-workspot-control-from-the-gallery"></a>Ajout de Workspot Control depuis la galerie
Pour configurer l’intégration de Workspot Control à Azure AD, vous devez ajouter Workspot Control à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workspot Control à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Workspot Control**, sélectionnez **Workspot Control** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Workspot Control, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Workspot Control correspondant à l’utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Workspot Control associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Workspot Control, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Workspot Control](#create-a-workspot-control-test-user)** pour avoir un équivalent de Britta Simon dans Workspot Control lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Workspot Control.

**Pour configurer l’authentification unique Azure AD avec Workspot Control, effectuez les étapes suivantes :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Workspot Control**, cliquez sur **Authentification unique**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Workspot Control](mailto:support@workspot.com). 

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Dans la section **Configurer Workspot Control**, copiez l’URL appropriée en fonction de vos besoins.

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Workspot Control en tant qu’administrateur de la sécurité.

8. Sur la barre d'outils située en haut de la page, cliquez sur  **Configuration**, puis sur  **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Dans la page **Security Assertion Markup Language Configuration** (Configuration SAML), effectuez les étapes suivantes :
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. Dans la zone de texte **Entity ID** (Identificateur d’entité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.   

    Dans la zone de texte **Signon Service URL** (URL de service de connexion), collez l’**URL de connexion** que vous avez copiée sur le portail Azure.

    c. Dans la zone de texte **Logout Service URL** (URL de service de déconnexion), collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure. 

    d. Cliquez sur le bouton **Update File** (Mettre à jour le fichier) pour charger le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure dans le certificat X509.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-workspot-control-test-user"></a>Créer un utilisateur de test Workspot Control

Pour se connecter à Workspot Control, les utilisateurs d’Azure AD doivent être attribués dans Workspot Control. Dans Workspot Control, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Workspot Control en tant qu’administrateur de la sécurité.

2. Sur la barre d'outils située en haut de la page, cliquez sur  **Utilisateurs**, puis sur  **Ajouter un utilisateur**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Dans la page **Add a new user** (Ajouter un nouvel utilisateur), effectuez les étapes suivantes :

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), saisissez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    d. Sélectionnez le rôle d’utilisateur approprié dans la liste déroulante **Role**.

    e. Sélectionnez le groupe d’utilisateurs approprié dans la liste déroulante **Group**.

    f. Cliquez sur **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workspot Control.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Workspot Control**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Workspot Control dans le volet d’accès, vous devez être connecté automatiquement à votre application Workspot Control.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
