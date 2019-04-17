---
title: 'Didacticiel : Intégration d’Azure Active Directory à Way We Do | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565512"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Didacticiel : Intégration d’Azure Active Directory à Way We Do

Dans ce tutoriel, vous apprenez à intégrer Way We Do à Azure Active Directory (Azure AD).
L’intégration de Way We Do à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Way We Do.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Way We Do (authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Way We Do, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement Way We Do avec l’authentification unique activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Way We Do prend en charge l’authentification unique (SSO) lancée par le **fournisseur de services**

* Way We Do prend en charge le provisionnement d’utilisateurs **Juste-à-temps**

## <a name="adding-way-we-do-from-the-gallery"></a>Ajout de Way We Do à partir de la galerie

Pour configurer l’intégration de Way We Do à Azure AD, vous devez ajouter Way We Do à partir de la galerie, dans votre liste d’applications SaaS gérées.

**Pour ajouter Way We Do à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Way We Do**, sélectionnez **Way We Do** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Way We Do dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et tester l’authentification unique Azure AD avec Way We Do au moyen d’un utilisateur de test, **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Way We Do associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Way We Do, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)**  : pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Way We Do](#configure-way-we-do-single-sign-on)**  : pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Way We Do](#create-way-we-do-test-user)**  : pour avoir un équivalent de Britta Simon dans Way We Do lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Way We Do, effectuez les étapes suivantes :

1. Dans le [Portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Way We Do**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Way We Do](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Way We Do](mailto:support@waywedo.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, à la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Raw)** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer Way We Do**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-way-we-do-single-sign-on"></a>Configurer l’authentification unique Way We Do

1. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Way We Do comme administrateur de sécurité.

2. Cliquez sur l’**icône représentant une personne** dans le coin supérieur droit d’une page de Way We Do, puis cliquez sur **Account** (Compte) dans le menu déroulant.

    ![Compte Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Cliquez sur l’**icône de menu** pour ouvrir le menu de navigation de commande, puis cliquez sur **Single Sign On** (Authentification unique).

    ![Authentification unique Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Dans la page **Single sign-on setup** (Configuration de l’authentification unique), procédez comme suit :

    ![Enregistrer Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Cliquez sur le bouton bascule de **Turn on single sign-on** (Activer l’authentification unique) pour choisir **Yes** (Oui) et ainsi activer l’authentification unique.

    b. Dans la zone de texte **Single sign-on name** (Nom de l’authentification unique), entrez votre nom.

    c. Dans la zone de texte **Entity ID**, collez l’**Identificateur Azure AD** que vous avez copié dans le portail Azure.

    d. Dans la zone de texte **SAML SSO URL** (URL d’authentification unique SAML), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    e. Chargez le certificat en cliquant sur le bouton **Select** (sélectionner) situé en regard de **Certificate** (Certificat).

    f. **Paramètres facultatifs** -
    
    * Enable Passwords (Activer les mots de passe) - Lorsque cette option est désactivée, le mot de passe habituel fonctionne pour Way We Do de telle sorte que les utilisateurs n’ont pas d’autre choix que celui d’utiliser l’authentification unique.

    * Enable Auto-provisioning (Activer le provisionnement automatique) - Lorsque cette option est activée, l’adresse e-mail utilisée pour l’authentification est automatiquement comparée à la liste des utilisateurs dans Way We Do. Si l’adresse e-mail ne correspond pas à un utilisateur actif dans Way We Do, un nouveau compte d’utilisateur pour la personne se connectant est ajouté, dans lequel il lui est demandé de renseigner les informations manquantes.

      > [!NOTE]
      > Les utilisateurs ajoutés via l’authentification unique sont ajoutés en tant qu’utilisateurs généraux et aucun rôle ne leur est attribué dans le système. Un administrateur est en mesure d’accéder et de modifier son rôle de sécurité en tant qu’éditeur ou administrateur, il peut également affecter un ou plusieurs rôles d’organigramme. 

    g. Cliquez sur **Save** pour enregistrer vos paramètres.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Way We Do.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Way We Do**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Way We Do**.

    ![Lien Way We Do dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-way-we-do-test-user"></a>Créer un utilisateur de test Way We Do

Dans cette section, l’utilisateur Britta Simon est créé dans Way We Do. Way We Do prend en charge le provisionnement d’utilisateurs juste-à-temps, activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Way We Do, un nouveau est créé après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support technique du client Way We Do](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Way We Do dans le panneau d’accès, vous devez être automatiquement connecté à l’application Way We Do pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

