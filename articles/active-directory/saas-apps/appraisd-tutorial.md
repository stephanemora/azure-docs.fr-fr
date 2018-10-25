---
title: 'Tutoriel : Intégration d’Azure Active Directory à Appraisd | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118195"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Tutoriel : Intégration d’Azure Active Directory à Appraisd

Dans ce tutoriel, vous allez apprendre à intégrer Appraisd à Azure Active Directory (Azure AD).

L’intégration d’Appraisd à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Appraisd.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Appraisd (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Appraisd, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Appraisd pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Appraisd depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-appraisd-from-the-gallery"></a>Ajout d’Appraisd depuis la galerie
Pour configurer l’intégration d’Appraisd à Azure AD, vous devez ajouter Appraisd, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Appraisd à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Appraisd**, sélectionnez **Appraisd** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Appraisd au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Appraisd équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Appraisd associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Appraisd, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Appraisd](#create-an-appraisd-test-user)** pour avoir un équivalent de Britta Simon dans Appraisd, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Appraisd.

**Pour configurer l’authentification unique Azure AD avec Appraisd, effectuez les étapes suivantes :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Appraisd**, sélectionnez **Authentification unique**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Cliquez sur **Définir des URL supplémentaires**. 

    b. Dans la zone de texte **État de relais**, tapez une URL : `<TENANTCODE>`

    c. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de services**, dans la zone de texte **URL d’authentification**, tapez une URL au format suivant : `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Vous obtenez les valeurs d’URL de connexion et d’état de relais réelles dans la page de configuration de l’authentification unique pour Appraisd, comme expliqué plus loin dans le tutoriel.
    
5. L’application Appraisd attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs d’utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et effectuez les étapes suivantes :
    
    a. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Dans la liste **Attribut de la source**, sélectionnez la valeur de l’attribut.

    c. Cliquez sur **Enregistrer**. 

7. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Dans la section **Configurer Appraisd**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure Active Directory

    c. URL de déconnexion

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous à Appraisd en tant qu’administrateur de la sécurité.

10. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à **Configuration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. Sur le côté gauche du menu, cliquez sur **SAML single sign-on** (Authentification unique SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Dans la page **SAML 2.0 Single Sign-On configuration** (Configuration de l’authentification unique SAML 2.0), effectuez les étapes suivantes :
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiez la valeur **Default Relay State** (État de relais par défaut) et collez-la dans la zone de texte **État de relais** dans **Configuration SAML de base** sur le portail Azure.

    b. Copiez la valeur **Service-initiated login URL** (URL de connexion démarrée par le service) et collez-la dans la zone de texte **État de relais** dans **Configuration SAML de base** sur le portail Azure.

13. Faites défiler la même page vers le bas jusqu’à **Identifying users** (Identification des utilisateurs), puis effectuez les étapes suivantes :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    b. Dans la zone de texte **Identity Provider Issuer URL** (URL de l’émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    c. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat X.509** et cliquez sur **enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-appraisd-test-user"></a>Créer un utilisateur de test Appraisd

Pour se connecter à Appraisd, les utilisateurs Azure AD doivent être attribués dans Appraisd. Dans Appraisd, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Appraisd en tant qu’administrateur de la sécurité.

2. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à **Administration centre** (Centre d’administration).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Dans la barre d’outils en haut de la page, cliquez sur **People** (Personnes), puis accédez à **Add a new user** (Ajouter un nouvel utilisateur).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Dans la page **Add a new user** (Ajouter un nouvel utilisateur), effectuez les étapes suivantes :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. Dans la zone de texte **First name** (Prénom), tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    d. Cliquez sur **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Appraisd.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Appraisd dans le volet d’accès, vous devez être connecté automatiquement à votre application Appraisd.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
