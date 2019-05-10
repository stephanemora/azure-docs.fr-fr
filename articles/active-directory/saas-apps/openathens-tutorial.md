---
title: 'Didacticiel : Intégration d’Azure Active Directory à OpenAthens | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cbb1bc64d923add0b1fd38749299714a9093633
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408033"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Didacticiel : Intégration d’Azure Active Directory à OpenAthens

Dans ce didacticiel, vous allez apprendre à intégrer OpenAthens dans Azure Active Directory (Azure AD).
L’intégration d’OpenAthens dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à OpenAthens.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à OpenAthens (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à OpenAthens, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement OpenAthens pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* OpenAthens prend en charge l’authentification unique initiée par le **fournisseur d’identité**

* OpenAthens prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-openathens-from-the-gallery"></a>Ajout d’OpenAthens à partir de la galerie

Pour configurer l’intégration d’OpenAthens à Azure AD, vous devez ajouter OpenAthens à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter OpenAthens à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **OpenAthens**, sélectionnez **OpenAthens** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![OpenAthens dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’OpenAthens avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur OpenAthens associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD auprès d’OpenAthens, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique OpenAthens](#configure-openathens-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test OpenAthens](#create-openathens-test-user)** pour avoir un équivalent de Britta Simon dans OpenAthens lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec OpenAthens, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **OpenAthens**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

5. Dans la section **Configuration SAML de base**, chargez le **fichier de métadonnées du fournisseur de services** ; les étapes pour effectuer cette opération sont indiquées plus loin dans ce tutoriel.

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![charger les métadonnées pour openathens](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![rechercher et charger les métadonnées pour openathens](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, la valeur **Identificateur** est automatiquement renseignée dans la zone de texte de la section **Configuration SAML de base** :

    ![Informations d’authentification unique dans Domaine et URL OpenAthens](common/idp-identifier.png)

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Configurer l’authentification unique OpenAthens

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise OpenAthens en tant qu’administrateur.

2. Sélectionnez **Connexions** dans la liste sous l’onglet **Gestion**. 

    ![Configurer l’authentification unique](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Sélectionnez **SAML 1.1/2.0**, puis le bouton **Configurer**.

    ![Configurer l’authentification unique](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Pour ajouter la configuration, sélectionnez le bouton **Parcourir** pour charger le fichier .xml de métadonnées que vous avez téléchargé à partir du portail Azure, puis sélectionnez **Ajouter**.

    ![Configurer l’authentification unique](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Effectuez les étapes suivantes sous l’onglet **Détails**.

    ![Configurer l’authentification unique](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Dans **Display name mapping** (Mappage de nom d’affichage), sélectionnez **Use Attribute** (Utiliser l’attribut).

    b. Dans la zone de texte **Display name attribute** (Attribut de nom d’affichage), entrez la valeur `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Dans **Unique user mapping** (Mappage d’utilisateur unique), sélectionnez **Use Attribute** (Utiliser l’attribut).

    d. Dans la zone de texte **Unique user attribute** (Attribut d’utilisateur unique), entrez la valeur `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Dans **État**, cochez les trois cases.

    f. Dans **Create local accounts** (Créer des comptes locaux), sélectionnez **automatically** (Automatiquement).

    g. Sélectionnez **Enregistrer les modifications**.

    h. Sous l’onglet **</> Relying Party** (Partie de confiance), copiez l’**URL des métadonnées** et ouvrez-la dans le navigateur pour télécharger le **fichier XML de métadonnées du fournisseur de services**. Chargez ce fichier de métadonnées du fournisseur de services dans la section **Configuration SAML de base** dans Azure AD.

    ![Configurer l’authentification unique](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous permettez à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à OpenAthens.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **OpenAthens**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **OpenAthens**.

    ![Lien OpenAthens dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-openathens-test-user"></a>Créer un utilisateur de test OpenAthens

Dans cette section, un utilisateur appelé Britta Simon est créé dans OpenAthens. OpenAthens prend en charge l’**attribution d’utilisateurs juste-à-temps**, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans OpenAthens, il en est créé un après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette OpenAthens dans le panneau d’accès doit vous connecter automatiquement à l’application OpenAthens pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

