---
title: 'Tutoriel : Intégration d’Azure Active Directory à Aha! | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a68deb2cb6de90c577f2d828e24a1747662c7e2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162201"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Didacticiel : Intégration d’Azure Active Directory à Aha!

Dans ce didacticiel, vous allez apprendre à intégrer Aha! à Azure Active Directory (Azure AD).
L’intégration d’Aha! à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Aha!.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Aha ! (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Aha!, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Aha! pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Aha! prend en charge l’authentification unique lancée par le **fournisseur de services**
* Aha! prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-aha-from-the-gallery"></a>Ajout d’Aha! à partir de la galerie

Pour configurer l’intégration d’Aha! à Azure AD, vous devez ajouter Aha! depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Aha! à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Aha !**, sélectionnez **Aha !** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Aha! dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Aha! sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Aha! associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Aha!, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Aha ! ](#configure-aha-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Aha!](#create-aha-test-user)** pour avoir un équivalent de Britta Simon dans Aha!, lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès d’Aha!, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Aha!**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Aha! Informations d’authentification unique dans Domaine et URL](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.aha.io/session/new`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<companyname>.aha.io`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [ l’équipe du support technique d’Aha!](https://www.aha.io/company/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Aha!**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-aha-single-sign-on"></a>Configurer Aha ! Single Sign on

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Aha! en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/aha-tutorial/IC798950.png "Paramètres")

3. Cliquez sur **Account**.
  
    ![Profil](./media/aha-tutorial/IC798951.png "Profil")

4. Cliquez sur **Security and single sign-on**.

    ![Sécurité et authentification unique](./media/aha-tutorial/IC798952.png "Sécurité et authentification unique")

5. Dans la section **Single Sign-On**, pour **Identity Provider**, sélectionnez **SAML2.0**.

    ![Sécurité et authentification unique](./media/aha-tutorial/IC798953.png "Sécurité et authentification unique")

6. Dans la page de configuration **Single Sign on** , procédez comme suit :

    ![Authentification unique](./media/aha-tutorial/IC798954.png "Authentification unique")

    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.

    b. Pour **Configure using**, sélectionnez **Metadata File**.

    c. Pour charger le fichier de métadonnées téléchargé, cliquez sur **Browse**.

    d. Cliquez sur **Update**.

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

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Aha!.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Aha!**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Aha!**.

    ![Lien Aha! dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-aha-test-user"></a>Création d’un utilisateur de test Aha!

Dans cette section, un utilisateur appelé Britta Simon est créé dans Aha!. Aha! prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Aha!, il en est créé un après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Aha! dans le volet d’accès, vous devez vous connecter automatiquement à l’application Aha! pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
