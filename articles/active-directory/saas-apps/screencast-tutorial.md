---
title: 'Didacticiel : Intégration d’Azure Active Directory à Screencast-O-Matic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 836ef454e5bdb14fcc3f519f9c6d9f6dfb4b5703
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686316"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Didacticiel : Intégration d’Azure Active Directory à Screencast-O-Matic

Dans ce tutoriel, vous allez apprendre à intégrer Screencast-O-Matic à Azure Active Directory (Azure AD).
L’intégration de Screencast-O-Matic à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Screencast-O-Matic.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Screencast-O-Matic (par le biais de l’authentification unique) avec leur compte Azure Active Directory.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Screencast-O-Matic, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement Screencast-O-Matic pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Screencast-O-Matic prend en charge l’authentification unique initiée par le **fournisseur de services**
* Screencast-O-Matic prend en charge l’attribution d’utilisateurs **juste-à-temps**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Ajout de Screencast-O-Matic à partir de la galerie

Pour configurer l’intégration de Screencast-O-Matic dans Azure AD, vous devez ajouter Screencast-O-Matic depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Screencast-O-Matic à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Screencast-O-Matic**, sélectionnez **Screencast-O-Matic** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Screencast-O-Matic dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure Active Directory avec Screencast-O-Matic, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure Active Directory et l’utilisateur Screencast-O-Matic associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Screencast-O-Matic, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Screencast-O-Matic](#configure-screencast-o-matic-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Screencast-O-Matic](#create-screencast-o-matic-test-user)** pour avoir un équivalent de Britta Simon dans Screencast-O-Matic qui est lié à la représentation de l’utilisateur Azure Active Directory.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure Active Directory avec Screencast-O-Matic, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Screencast-O-Matic**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Screencast-O-Matic](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique de Screencast-O-Matic](mailto:support@screencast-o-matic.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Screencast-O-Matic**, copiez la ou les URL appropriées en fonction de vos exigences.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-screencast-o-matic-single-sign-on"></a>Abonnement Screencast-O-Matic pour lequel l’authentification unique est activée

1. Dans une autre fenêtre de navigateur web, connectez-vous à Screencast-O-Matic en tant qu’administrateur.

2. Cliquez sur **Abonnement**.

    ![L’abonnement](./media/screencast-tutorial/tutorial_screencast_sub.png)

3. Dans la section **Page d’accès**, cliquez sur **Configuration**.

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_setup.png)

4. Dans la **page Configurer l’accès**, procédez comme suit :

   * Dans la section **URL d’accès**, tapez le nom de votre instance dans la zone de texte spécifiée.

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_access.png)

   * Sélectionnez **Demander un utilisateur de domaine** dans la section **Restriction d’utilisateur SAML (facultatif)** .

   * Sous **Charger le fichier XML des métadonnées IDP**, cliquez sur **Sélectionner le fichier** pour charger les métadonnées que vous avez téléchargées à partir du portail Azure.

   * Cliquez sur **OK**.

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_save.png)

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Screencast-O-Matic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Screencast-O-Matic**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Screencast-O-Matic**.

    ![Lien Screencast-O-Matic dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-screencast-o-matic-test-user"></a>Créer un utilisateur de test Screencast-O-Matic

Dans cette section, un utilisateur nommé Britta Simon est créé dans Screencast-O-Matic. Screencast-O-Matic prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Screencast-O-Matic, un nouvel utilisateur est créé après l’authentification. Si vous devez créer un utilisateur manuellement, contactez  [l’équipe de support client Screencast-O-Matic](mailto:support@screencast-o-matic.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Screencast-O-Matic dans le volet d’accès, vous devez être connecté automatiquement à l’application Screencast-O-Matic pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
