---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160632"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal

Dans ce didacticiel, vous allez apprendre à intégrer Mimecast Personal Portal avec Azure Active Directory (Azure AD).
L’intégration de Mimecast Personal Portal avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Mimecast Personal Portal.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Mimecast Personal Portal (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD avec Mimecast Personal Portal, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Mimecast Personal Portal pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Mimecast Personal Portal prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Ajout de Mimecast Personal Portal à partir de la galerie

Pour configurer l’intégration de Mimecast Personal Portal à Azure AD, vous devez ajouter Mimecast Personal Portal à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Mimecast Personal Portal à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Mimecast Personal Portal**, sélectionnez **Mimecast Personal Portal** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Mimecast Personal Portal dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mimecast Personal Portal sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Mimecast Personal Portal associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Mimecast Personal Portal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** pour obtenir un équivalent de Britta Simon dans Mimecast Personal Portal lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Mimecast Personal Portal, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Mimecast Personal Portal**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Mimecast Personal Portal](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL : 

    | Région  |  Valeur | 
    | --------------- | --------------- | 
    | Europe          | `https://eu-api.mimecast.com/login/saml`|
    | États-Unis   | `https://us-api.mimecast.com/login/saml`|
    | Afrique du Sud    | `https://za-api.mimecast.com/login/saml`|
    | Australie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :

    | Région  |  Valeur | 
    | --------------- | --------------- |
    | Europe          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | États-Unis   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Afrique du Sud    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : 

    | Région  |  Valeur | 
    | --------------- | --------------- | 
    | Europe          | `https://eu-api.mimecast.com/login/saml`|
    | États-Unis   | `https://us-api.mimecast.com/login/saml`|
    | Afrique du Sud    | `https://za-api.mimecast.com/login/saml`|
    | Australie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Pour obtenir la valeur, contactez l’[équipe de support technique Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Mimecast Personal Portal**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configurer l’authentification unique Mimecast Personal Portal

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Personal Portal en tant qu’administrateur.

2. Accédez à **Services \> Application**.
   
    ![Applications](./media/mimecast-personal-portal-tutorial/ic794998.png "Applications")

3. Cliquez sur **Authentication Profiles**.
   
    ![Profils d’authentification](./media/mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles")

4. Cliquez sur **New Authentication Profile**.
   
    ![Nouveau profil d’authentification](./media/mimecast-personal-portal-tutorial/ic795000.png "Nouveau profil d’authentification")

5. Dans la section **Authentication Profile** , procédez comme suit :
   
    ![Profil d’authentification](./media/mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile")
   
    a. Dans la zone de texte **Description** , indiquez un nom pour votre configuration.
   
    b. Sélectionnez **Enforce SAML Authentication for Mimecast Personal Portal**.
   
    c. Pour **Provider**, sélectionnez **Azure Active Directory**.
   
    d. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez la valeur de l’**identifiant Azure AD**, que vous avez copiée dans le portail Azure.
   
    e. Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion**, que vous avez copiée dans le portail Azure.
   
    f. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur **URL de déconnexion**, que vous avez copiée dans le portail Azure.

    g. Ouvrez votre certificat codé **en base 64** dans le bloc-notes téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)** (Certificat de fournisseur d’identité (métadonnées)).

    h. Sélectionnez **Allow Single Sign On**.
   
    i. Cliquez sur **Enregistrer**.

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
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mimecast Personal Portal.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Mimecast Personal Portal**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Mimecast Personal Portal**.

    ![Lien Mimecast Personal Portal dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-mimecast-personal-portal-test-user"></a>Créer un utilisateur de test Mimecast Personal Portal

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Personal Portal, vous devez les approvisionner dans Mimecast Personal Portal. Dans le cas de Mimecast Personal Portal, l’approvisionnement est une tâche manuelle.

Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à **Mimecast Personal Portal** en tant qu’administrateur.

2. Accédez à **Directories \> Internal**.
   
    ![Directories](./media/mimecast-personal-portal-tutorial/ic795003.png "Répertoires")

3. Cliquez sur **Register New Domain**.
   
    ![Inscrire un nouveau domaine](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain")

4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
    ![Nouvelle adresse](./media/mimecast-personal-portal-tutorial/ic795005.png "New Address")

5. Dans la boîte de dialogue Nouvelle Adresse, procédez comme suit pour un compte Azure AD valide que vous souhaitez approvisionner :
   
    ![Save](./media/mimecast-personal-portal-tutorial/ic795006.png "Enregistrer")
   
    a. Dans la zone de texte **Adresse e-mail**, tapez l’**adresse e-mail** de l’utilisateur sous la forme **BrittaSimon\@contoso.com**.
    
    b. Dans la zone de texte **Nom global**, tapez le **nom d’utilisateur** sous la forme **BrittaSimon**.

    c. Dans les zones de texte **Mot de passe** et **Confirmer le mot de passe**, type de le **mot de passe** de l’utilisateur.
   
    b. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Mimecast Personal Portal pour approvisionner des comptes d’utilisateur Azure AD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Mimecast Personal Portal dans le panneau d’accès, vous devez être connecté automatiquement à l’application Mimecast Personal Portal pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

