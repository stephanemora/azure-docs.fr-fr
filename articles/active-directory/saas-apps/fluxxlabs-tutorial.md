---
title: 'Didacticiel : Intégration d’Azure Active Directory à Fluxx Labs | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102388"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Didacticiel : Intégration d’Azure Active Directory à Fluxx Labs

Dans ce didacticiel, vous allez apprendre à intégrer Fluxx Labs à Azure Active Directory (Azure AD).
L’intégration de Fluxx Labs à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Fluxx Labs.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Fluxx Labs (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à Fluxx Labs, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement Fluxx Labs pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Fluxx Labs prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-fluxx-labs-from-the-gallery"></a>Ajout de Fluxx Labs à partir de la galerie

Pour configurer l’intégration de Fluxx Labs à Azure AD, vous devez ajouter Fluxx Labs à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Fluxx Labs à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Fluxx Labs**, sélectionnez **Fluxx Labs** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Fluxx Labs dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Fluxx Labs pour un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Fluxx Labs associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Fluxx Labs, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Fluxx Labs](#configure-fluxx-labs-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Fluxx Labs](#create-fluxx-labs-test-user)** pour avoir un équivalent de Britta Simon dans Fluxx Labs lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Fluxx Labs, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Fluxx Labs**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Fluxx Labs](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe du support Fluxx Labs](mailto:travis@fluxxlabs.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Fluxx Labs**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-fluxx-labs-single-sign-on"></a>Configurer l’authentification unique pour Fluxx Labs

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

2. Sélectionnez **Admin** sous la section **Paramètres**.

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. Dans le volet Administration, sélectionnez **Plug-ins** > **Intégrations**, puis **SAML SSO-(Disabled)** (Authentification unique SAML-(Désactivée))

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. Dans la section d’attributs, procédez comme suit :

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Cochez la case **SAML SSO** (Authentification unique SAML).

    b. Dans la zone de texte **Request Path** (Chemin d’accès à la demande), saisissez **/auth/saml**.

    c. Dans la zone de texte **Callback Path** (Chemin de rappel), saisissez **/auth/saml/callback**.

    d. Dans la zone de texte **URL Assertion Consumer Service (URL d’authentification unique)** , entrez la valeur **URL de réponse** que vous avez saisie dans le portail Azure.

    e. Dans la zone de texte **Audience (ID d’entité SP)** , entrez la valeur **Identifier** que vous avez saisie dans le portail Azure.

    f. Dans la zone de texte **Identity Provider SSO Target URL** (URL cible d’authentification unique du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    g. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate (Certificat du fournisseur d’identité)** .

    h. Dans la zone de texte **Format de l’identificateur de nom**, saisissez la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Une fois le contenu enregistré, le champ apparaît vide pour des raisons de sécurité, mais la valeur est bien enregistrée dans la configuration.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez brittasimon@yourcompanydomain.extension. Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fluxx Labs.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Fluxx Labs**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Fluxx Labs**.

    ![Lien Fluxx Labs dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-fluxx-labs-test-user"></a>Créer un utilisateur de test Fluxx Labs

Pour permettre aux utilisateurs Azure AD de se connecter à Fluxx Labs, vous devez les provisionner dans Fluxx Labs. Dans le cas de Fluxx Labs, l’approvisionnement se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

2. Cliquez sur **l’icône** affichée ci-dessous.

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Dans le tableau de bord, cliquez sur l’icône affichée ci-dessous pour ouvrir la carte **New PEOPLE** (Nouvelles personnes).

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. Dans la section **NEW PEOPLE** (NOUVELLES PERSONNES), procédez comme suit :

    ![Configuration de Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs utilise une adresse e-mail comme identificateur unique pour les connexions avec authentification unique. Renseignez le champ **SSO UID** (UID SSO) avec l’adresse e-mail que l’utilisateur utilise pour la connexion avec authentification unique.

    b. Cliquez sur **Enregistrer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Fluxx Labs dans le volet d’accès, vous devez être connecté automatiquement à l’application Fluxx Labs pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

