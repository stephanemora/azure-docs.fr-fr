---
title: 'Didacticiel : Intégration d’Azure Active Directory à Lucidchart | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12b5984eb2fe4e54ffec7d6b4a2a6da202c70e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097805"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Didacticiel : Intégration d’Azure Active Directory à Lucidchart

Dans ce didacticiel, vous allez apprendre à intégrer Lucidchart à Azure Active Directory (Azure AD).
L’intégration d’Azure AD à Lucidchart offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Lucidchart.
* Vous pouvez permettre à vos utilisateurs d’être automatiquement connectés à Lucidchart (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Lucidchart, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Lucidchart pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Lucidchart prend en charge l’authentification unique initiée par le **fournisseur de services**
* Lucidchart prend en charge le provisionnement des utilisateurs **Juste-à-temps**

## <a name="adding-lucidchart-from-the-gallery"></a>Ajout de Lucidchart à partir de la galerie

Pour configurer l’intégration de Lucidchart à Azure AD, vous devez ajouter Lucidchart à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Lucidchart à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Lucidchart**, sélectionnez **Lucidchart** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Lucidchart dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Lucidchart, à l’aide d’un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Lucidchart associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Lucidchart, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique de Lucidchart](#configure-lucidchart-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer l’utilisateur de test Lucidchart](#create-lucidchart-test-user)** pour avoir dans Lucidchart un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de Lucidchart, effectuez les étapes suivantes :

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Lucidchart**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique relatives au domaine et aux URL Lucidchart](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL comme `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Set up Lucidchart** (Configurer Lucidchart), copiez l’URL ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-lucidchart-single-sign-on"></a>Configurer l’authentification unique de Lucidchart

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Lucidchart en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Team**.

    ![Équipe](./media/lucidchart-tutorial/ic791190.png "Équipe")

3. Cliquez sur **Applications \> Gérer SAML**.

    ![Gérer SAML](./media/lucidchart-tutorial/ic791191.png "Gérer SAML")

4. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    a. Sélectionnez **Enable SAML Authentication**, puis cliquez sur **Optional**.

    ![Paramètres d’authentification SAML](./media/lucidchart-tutorial/ic791192.png "Paramètres d’authentification SAML")

    b. Dans la zone de texte **Domain**, entrez votre domaine, puis cliquez sur **Change Certificate**.

    ![Modifier le certificat](./media/lucidchart-tutorial/ic791193.png "Modifier le certificat")

    c. Ouvrez votre fichier de métadonnées téléchargé, copiez son contenu, puis collez-le dans la zone de texte **Upload Metadata** .

    ![Charger les métadonnées](./media/lucidchart-tutorial/ic791194.png "Charger les métadonnées")

    d. Sélectionnez **Automatically Add new user to the team** (Ajouter automatiquement un utilisateur à l’équipe), puis cliquez sur **Enregistrer les modifications**.

    ![Enregistrer les modifications](./media/lucidchart-tutorial/ic791195.png "Enregistrer les modifications")

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lucidchart.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Lucidchart**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Lucidchart**.

    ![Lien Lucidchart dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-lucidchart-test-user"></a>Créer l’utilisateur de test Lucidchart

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lucidchart.  Lorsqu’un utilisateur tente de se connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie si cet utilisateur existe.  

Si aucun compte d’utilisateur n’est disponible, Lucidchart le crée automatiquement.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Lucidchart dans le volet d’accès doit vous connecter automatiquement à l’application Lucidchart pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
