---
title: 'Didacticiel : Intégration d’Azure Active Directory à Adaptive Insights | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: c217663c5752907e0b3d6372d4522f6aba982b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107405"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Didacticiel : Intégration d’Azure Active Directory à Adaptive Insights

Dans ce didacticiel, vous allez apprendre à intégrer Adaptive Insights dans Azure Active Directory (Azure AD).
L’intégration d’Adaptive Insights dans Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Adaptive Insights.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adaptive Insights (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans Adaptive Insights, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Adaptive Insights pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Adaptive Insights prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Ajout d’Adaptive Insights à partir de la galerie

Pour configurer l’intégration d’Adaptive Insights dans Azure AD, vous devez ajouter Adaptive Insights, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Adaptive Insights à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Adaptive Insights**, sélectionnez **Adaptive Insights** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Adaptive Insights dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adaptive Insights, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Adaptive Insights associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Adaptive Insights, vous devez suivre les indications des sections ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Adaptive Insights](#configure-adaptive-insights-single-sign-on)** pour définir les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créez un utilisateur de test Adaptive Insights](#create-adaptive-insights-test-user)** pour avoir un équivalent de Britta Simon dans Adaptive Insights, lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Adaptive Insights, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Adaptive Insights**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Adaptive Insights](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Vous pouvez obtenir les valeurs de l’Identificateur (ID d’entité) et l’URL de réponse sur la page **Paramètres d’authentification unique SAML** d’Adaptive Insights.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Adaptive Insights**, copiez la ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-adaptive-insights-single-sign-on"></a>Configurer l’authentification unique dans Adaptive Insights

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adaptive Insights en tant qu’administrateur.

2. Accédez à **Admin**.

    ![Administrateur](./media/adaptivesuite-tutorial/ic805644.png "Administrateur")

3. Dans la section **Users and Roles**, cliquez sur **Manage SAML SSO Settings**.

    ![Gérer les paramètres d’authentification unique de SAML](./media/adaptivesuite-tutorial/ic805645.png "Gérer les paramètres d’authentification unique de SAML")

4. Dans la page **SAML SSO Settings** , procédez comme suit :

    ![Paramètres d’authentification unique de SAML](./media/adaptivesuite-tutorial/ic805646.png "Paramètres d’authentification unique de SAML")

    a. Dans la zone de texte **Identity provider name** , attribuez un nom à votre configuration.

    b. Collez la valeur **Identificateur Azure AD** copiée à partir du portail Azure dans la zone de texte **ID d’entité du fournisseur d’identité**.

    c. Collez la valeur **URL de connexion** copiée à partir du portail Azure dans la zone de texte **URL SSO du fournisseur d’identité**.

    d. Collez la valeur **URL de déconnexion** copiée à partir du portail Azure dans la zone de texte **URL de déconnexion personnalisée**.

    e. Pour charger votre certificat téléchargé, cliquez sur **Choisir un fichier**.

    f. Sélectionnez les options suivantes :

     * Pour **SAML user id**, sélectionnez **User’s Adaptive Insights user name**.

     * Pour **SAML user id location**, sélectionnez **User id in NameID of Subject**.

     * Pour **SAML NameID format**, sélectionnez **Email address**.

     * Pour **Enable SAML**, sélectionnez **Allow SAML SSO and direct Adaptive Insights login**.

    g. Copiez l’**URL d’authentification unique Adaptive Insights** et collez-la dans les zones de texte **Identificateur (ID d’entité)** et **URL de réponse** dans la section **URL et domaine Adaptive Insights** dans le portail Azure.

    h. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, saisissez brittasimon@yourcompanydomain.extension. Par exemple : BrittaSimon@contoso.com.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adaptive Insights.

1. Dans le portail Microsoft Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Adaptive Insights**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Adaptive Insights**.

    ![Lien Adaptive Insights dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-adaptive-insights-test-user"></a>Créer un utilisateur de test Adaptive Insights

Pour permettre aux utilisateurs Azure AD de se connecter à Adaptive Insights, vous devez les approvisionner dans Adaptive Insights. Dans le cas d’Adaptive Insights, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Adaptive Insights** en tant qu’administrateur.

2. Accédez à **Admin**.

   ![Administrateur](./media/adaptivesuite-tutorial/IC805644.png "Administrateur")

3. Dans la section **Users and Roles**, cliquez sur **Add User**.

   ![Ajouter un utilisateur](./media/adaptivesuite-tutorial/IC805648.png "Ajouter un utilisateur")

4. Dans la section **New User**, procédez comme suit :

   ![Envoyer](./media/adaptivesuite-tutorial/IC805649.png "Envoyer")

   a. Tapez le nom, l’identifiant de connexion, l’adresse de messagerie et le mot de passe de l’utilisateur Azure Active Directory valide que vous souhaitez renseigner dans les zones de texte correspondantes, à savoir, **Name**, **Login**, **Email** et **Password**.

   b. Sélectionnez un **rôle**.

   c. Cliquez sur **Envoyer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Adaptive Insights fourni par ce service pour approvisionner des comptes d’utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Adaptive Insights dans le volet d’accès, vous devez être connecté automatiquement à l’application Adaptive Insights pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)