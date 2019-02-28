---
title: 'Didacticiel : Intégration d’Azure Active Directory à Help Scout | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3650704cd3d01d1cce21a665f3731a00a2107ab7
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865720"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutoriel : Intégration d’Azure Active Directory à Help Scout

Dans ce didacticiel, vous allez apprendre à intégrer Help Scout à Azure Active Directory (Azure AD).
L’intégration de Help Scout à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Help Scout.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Help Scout (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Help Scout, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Help Scout pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Help Scout prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* Help Scout prend en charge l’attribution d’utilisateurs **juste-à-temps**

## <a name="adding-help-scout-from-the-gallery"></a>Ajout de Help Scout à partir de la galerie

Pour configurer l’intégration de Help Scout à Azure AD, vous devez ajouter Help Scout à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Help Scout à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Help Scout**, sélectionnez **Help Scout** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Help Scout dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Help Scout, à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Help Scout associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Help Scout, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Help Scout](#configure-help-scout-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer l’utilisateur de test Help Scout](#create-help-scout-test-user)** pour avoir dans Help Scout un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Help Scout, effectuez les étapes suivantes :

1. Dans la page d’intégration de l’application **Help Scout** sur le [portail Azure](https://portal.azure.com/), sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Help Scout](common/idp-intiated.png)

    a. **Identificateur** correspond à **Audience URI (Service Provider Entity ID)** (URI d’audience (ID d’entité de fournisseur de services)) Help Scout, qui commence par `urn:`

    b. **URL de réponse** correspond à **Post-back URL (Assertion Consumer Service URL)** (URL de publication (URL Assertion Consumer Service)) Help Scout, qui commence par `https://` 

    > [!NOTE]
    > Les valeurs de ces URL servent uniquement à des fins de démonstration. Vous devez les remplacer par l’URL de réponse et l’identificateur réels. Vous obtenez ces valeurs à partir de l’onglet **Authentification unique** sous la section Authentification, qui est décrite plus loin dans le didacticiel.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Help Scout](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://secure.helpscout.net/members/login/`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Help Scout**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-help-scout-single-sign-on"></a>Configurer l’authentification unique Help Scout

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Help Scout en tant qu’administrateur.

2. Cliquez sur **Manage** (Gérer) dans le menu supérieur, puis sélectionnez **Company** dans le menu déroulant.

    ![Configurer l'authentification unique](./media/helpscout-tutorial/settings1.png)

3. Sélectionnez **Authentication** à partir du volet de navigation gauche.

    ![Configurer l'authentification unique](./media/helpscout-tutorial/settings2.png)

4. Vous accédez ainsi à la section des paramètres SAML où vous effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/helpscout-tutorial/settings3.png)

    a. Copiez la valeur de **Post-back URL (Assertion Consumer Service URL)** (URL de publication (URL Assertion Consumer Service)) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** sur le portail Azure.

    b. Copiez la valeur de **Audience URI (Service Provider Entity ID)** (URL d’audience (ID d’entité de fournisseur de services)) et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** sur le portail Azure.

5. Activez **Activer SAML** et effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/helpscout-tutorial/settings4.png)

    a. Dans la zone de texte  **URL d’authentification unique** , collez la valeur de l’ **URL de connexion** que vous avez copiée dans le portail Azure.

    b. Cliquez sur **Charger le certificat** pour charger le **certificat (en base64)** téléchargé à partir du portail Azure.

    c. Entrez les domaines de messagerie de votre organisation, par exemple `contoso.com`, dans la zone de texte **Email Domains** (Domaines de messagerie). Vous pouvez séparer plusieurs domaines par une virgule. À tout moment, un utilisateur ou administrateur Help Scout qui entre dans ce domaine spécifique dans la [page de connexion d’Help Scout](https://secure.helpscout.net/members/login/) est acheminé vers le fournisseur d’identité pour être authentifié avec ses informations d’identification.

    d. Enfin, vous pouvez activer **Force SAML Sign-on** (Forcer l’authentification SAML) si vous souhaitez que les utilisateurs se connectent uniquement à Help Scout via cette méthode. Si vous voulez toujours leur donner la possibilité de se connecter avec leurs informations d’identification Help Scout, vous pouvez laisser cette option désactivée. Même si elle est activée, un propriétaire de compte sera toujours en mesure de se connecter à Help Scout avec son mot de passe de compte.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Help Scout.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Help Scout**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Help Scout**.

    ![Lien Help Scout dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-help-scout-test-user"></a>Créer l’utilisateur de test Help Scout

Dans cette section, un utilisateur appelé Britta Simon est créé dans Help Scout. Help Scout prend en charge l’attribution d’utilisateurs juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Help Scout, il en est créé un après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Help Scout dans le volet d’accès doit vous connecter automatiquement à l’application Help Scout pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)