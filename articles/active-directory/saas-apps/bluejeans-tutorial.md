---
title: 'Tutoriel : Intégration d’Azure Active Directory à BlueJeans | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1e4b971c92da91c6a62a5d8b38292a1d5679deb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167216"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutoriel : Intégration d’Azure Active Directory à BlueJeans

Dans ce didacticiel, vous allez apprendre à intégrer BlueJeans à Azure Active Directory (Azure AD).
L’intégration de BlueJeans à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à BlueJeans.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à BlueJeans (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à BlueJeans, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement BlueJeans pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AirWatch prend en charge l’authentification unique initiée par **SP**

* BlueJeans prend en charge [**l’approvisionnement** automatique d’utilisateurs](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Ajout de BlueJeans à partir de la galerie

Pour configurer l’intégration de BlueJeans à Azure AD, vous devez ajouter BlueJeans à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter BlueJeans à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **BlueJeans**, sélectionnez **BlueJeans** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![BlueJeans dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec BlueJeans avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur BlueJeans associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec BlueJeans, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique BlueJeans](#configure-bluejeans-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test BlueJeans](#create-bluejeans-test-user)** pour avoir un équivalent de Britta Simon dans BlueJeans lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec BlueJeans, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **BlueJeans**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL BlueJeans](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support client BlueJeans](https://support.bluejeans.com/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer BlueJeans**, copiez l’URL appropriée (ou les URL) en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-bluejeans-single-sign-on"></a>Configurer l’authentification unique BlueJeans

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2. Accédez à **ADMIN \> GROUP SETTINGS \> SECURITY**.

    ![Administrateur](./media/bluejeans-tutorial/IC785868.png "Administrateur")

3. Dans la section **SECURITY**, procédez comme suit :

    ![Authentification unique SAML](./media/bluejeans-tutorial/IC785869.png "Authentification unique SAML")

    a. Sélectionnez **SAML Single Sign On**.

    b. Sélectionnez **Enable automatic provisioning**.

4. Poursuivez en procédant comme suit :

    ![Chemin d’accès du certificat](./media/bluejeans-tutorial/IC785870.png "Chemin d’accès du certificat")

    a. Cliquez sur **Choose File** (Choisir un fichier) pour charger le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure.

    b. Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Password Change URL** (URL de modification de mot de passe), collez la valeur de l’**URL de modification de mot de passe** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

5. Poursuivez en procédant comme suit :

    ![Enregistrer les modifications](./media/bluejeans-tutorial/IC785874.png "Enregistrer les modifications")

    a. Dans la zone de texte **User ID** (ID utilisateur), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Dans la zone de texte **Email**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BlueJeans.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **BlueJeans**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Lien BlueJeans dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-bluejeans-test-user"></a>Création d’un utilisateur de test BlueJeans

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans BlueJeans. BlueJeans prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez [ici](bluejeans-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2. Accédez à **ADMIN \> MANAGE USERS \> ADD USER**.

    ![Administrateur](./media/bluejeans-tutorial/IC785877.png "Administrateur")

    >[!IMPORTANT]
    >L’onglet **ADD USER** est disponible seulement si, sous l’onglet **SECURITY**, l’option **Enable automatic provisioning** (Activer l’approvisionnement automatique) est décochée. 

3. Dans la section **ADD USER** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur](./media/bluejeans-tutorial/IC785886.png "Ajouter un utilisateur")

    a. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), saisissez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Pick a BlueJeans Username** (Choisir un nom d’utilisateur BlueJeans), entrez le nom de l’utilisateur, comme par exemple **Brittasimon**

    d. Dans la zone de texte **Create a Password** (Créer un mot de passe), entrez votre mot de passe.

    e. Dans la zone de texte **Company** (Entreprise), entrez votre entreprise.

    f. Dans la zone de texte **Adresse e-mail**, saisissez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    g. Dans la zone de texte **Create a BlueJeans Meeting I.D**  (Créer un ID de réunion BlueJeans), entrez votre ID de réunion.

    h. Dans la zone de texte **Pick a Moderator Passcode** (Choisir un code secret de modérateur), entrez votre code secret.

    i. Cliquez sur **CONTINUE** (Continuer).

    ![Ajouter un utilisateur](./media/bluejeans-tutorial/IC785887.png "Ajouter un utilisateur")

    J. Cliquez sur**ADD USER** (Ajouter un utilisateur).

> [!NOTE]
> Vous pouvez utiliser un autre outil ou une autre API de création de compte d’utilisateur fournis par BlueJeans pour provisionner des comptes d’utilisateur Azure AD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette BlueJeans dans le panneau d’accès doit vous connecter automatiquement à l’application BlueJeans pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

