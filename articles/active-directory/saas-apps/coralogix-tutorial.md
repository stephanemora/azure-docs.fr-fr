---
title: 'Didacticiel : Intégration d’Azure Active Directory à Coralogix | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158472"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Didacticiel : Intégration d’Azure Active Directory à Coralogix

Dans ce tutoriel, vous allez apprendre à intégrer Coralogix à Azure Active Directory (Azure AD).
L’intégration de Coralogix à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Coralogix.
* Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à Coralogix (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Coralogix, vous avez besoin des éléments suivants :

- Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).
- Un abonnement Coralogix pour lequel l’authentification unique est activée. 

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Coralogix prend en charge l’authentification unique lancée par le fournisseur de services.

## <a name="add-coralogix-from-the-gallery"></a>Ajouter Coralogix à partir de la galerie

Pour configurer l’intégration de Coralogix à Azure AD, ajoutez d’abord Coralogix à partir de la galerie à votre liste d’applications SaaS gérées.

Pour ajouter Coralogix à partir de la galerie, procédez comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Saisissez **Coralogix** dans la zone de recherche. Sélectionnez **Coralogix** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Coralogix dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Coralogix avec un utilisateur de test appelé Britta Simon.
Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Coralogix associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Coralogix, suivez d’abord les indications des modules suivants :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Configurer l’authentification unique Coralogix](#configure-coralogix-single-sign-on) pour configurer les paramètres de l’authentification unique côté application.
3. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Créer un utilisateur de test Coralogix](#create-a-coralogix-test-user) pour avoir un équivalent de Britta Simon dans Coralogix lié à la représentation Azure AD associée.
6. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de Coralogix, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Coralogix**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la boîte de dialogue **Configuration SAML de base**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Coralogix](common/sp-identifier.png)

    a. Dans la zone **URL de connexion**, entrez une URL au format suivant : `https://<SUBDOMAIN>.coralogix.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , entrez une URL, comme :
    
    `https://api.coralogix.com/saml/metadata.xml`

    or

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique Coralogix](mailto:info@coralogix.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Coralogix attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications utilisateur** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications à l’aide de l’icône **Modifier**. Vous pouvez également ajouter les revendications à l’aide de l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme illustré dans l’image précédente. Ensuite, effectuez les étapes suivantes :
    
    a. Sélectionnez **l’icône Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/coralogix-tutorial/tutorial_usermail.png) ![image](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. À partir de la liste **Choisir le format du nom de l’identificateur**, sélectionnez **Adresse e-mail**.

    c. Dans la liste **Attribut source**, sélectionnez **user.mail**.

    d. Sélectionnez **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins. Enregistrez-le ensuite sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Coralogix**, copiez la ou les URL appropriées.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-coralogix-single-sign-on"></a>Configurer l’authentification unique Coralogix

Pour configurer l’authentification unique côté **Coralogix**, envoyez le **XML des métadonnées de fédération** téléchargé et les URL copiées depuis le portail Azure à [l’équipe du support technique Coralogix](mailto:info@coralogix.com). L’équipe vérifiera que l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur**, en haut de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans la boîte de dialogue **Utilisateur**, procédez comme suit.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom de l’utilisateur**, entrez « brittasimon@yourcompanydomain.extension ». Par exemple, ici, entrez « brittasimon@contoso.com ».

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur figurant dans le champ **Mot de passe**.

    d. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Coralogix.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Coralogix**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Coralogix**.

    ![Lien Coralogix dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Sélectionnez le bouton **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs. Cliquez ensuite sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Select Role** (Sélectionner un rôle), sélectionnez le rôle approprié pour l’utilisateur dans la liste. Cliquez ensuite sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-a-coralogix-test-user"></a>Créer un utilisateur de test Coralogix

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Coralogix. Contactez  [l’équipe du support technique Coralogix](mailto:info@coralogix.com) pour ajouter les utilisateurs dans la plateforme Coralogix. Vous devez créer et activer les utilisateurs avant d’utiliser l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du portail MyApps.

Le fait de sélectionner la vignette Coralogix dans le portail MyApps doit vous connecter automatiquement à l’application Coralogix. Pour plus d’informations sur le portail MyApps, consultez [Qu’est-ce que le portail MyApps ?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

