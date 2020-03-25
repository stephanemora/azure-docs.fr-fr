---
title: 'Didacticiel : Intégration d’Azure Active Directory à Mimecast Admin Console | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160656"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Didacticiel : Intégration d’Azure Active Directory à Mimecast Admin Console

Dans ce didacticiel, vous allez apprendre à intégrer Mimecast Admin Console à Azure Active Directory (Azure AD).
L’intégration de Mimecast Admin Console à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Mimecast Admin Console.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Mimecast Admin Console (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à Mimecast Admin Console, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Mimecast Admin Console pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Mimecast Admin Console prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Ajout de Mimecast Admin Console à partir de la galerie

Pour configurer l’intégration de Mimecast Admin Console à Azure AD, vous devez ajouter Mimecast Admin Console à partir de la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Mimecast Admin Console à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Mimecast Admin Console**, sélectionnez **Mimecast Admin Console** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Mimecast Admin Console dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mimecast Admin Console sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Mimecast Admin Console associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Mimecast Admin Console, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Mimecast Admin Console](#configure-mimecast-admin-console-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Mimecast Admin Console](#create-mimecast-admin-console-test-user)** pour obtenir un équivalent de Britta Simon dans Mimecast Admin Console lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Mimecast Admin Console, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Mimecast Admin Console**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Mimecast Admin Console](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL :
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > L’URL d’ouverture de session est spécifique à la région.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Mimecast Admin Console**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Configurer l’authentification unique Mimecast Admin Console

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Admin Console en tant qu’administrateur.

2. Accédez à **Services \> Application**.

    ![Services](./media/mimecast-admin-console-tutorial/ic794998.png "Services")

3. Cliquez sur **Authentication Profiles**.

    ![Profils d’authentification](./media/mimecast-admin-console-tutorial/ic794999.png "Authentication Profiles")
    
4. Cliquez sur **New Authentication Profile**.

    ![Nouveaux profils d’authentification](./media/mimecast-admin-console-tutorial/ic795000.png "Nouveaux profils d’authentification")

5. Dans la section **Authentication Profile** , procédez comme suit :

    ![Profil d’authentification](./media/mimecast-admin-console-tutorial/ic795015.png "Authentication Profile")
    
    a. Dans la zone de texte **Description** , indiquez un nom pour votre configuration.
    
    b. Sélectionnez **Enforce SAML Authentication for Mimecast Admin Console**.
    
    c. Pour **Provider**, sélectionnez **Azure Active Directory**.
    
    d. Collez l’**Identificateur Azure AD**, que vous avez copié sur le portail Azure, dans la zone de texte **Issuer URL** (URL de l’émetteur).
    
    e. Collez l’**URL de connexion**, que vous avez copiée dans le portail Azure, dans la zone de texte **Login URL** (URL de connexion).

    f. Collez l’**URL de déconnexion**, que vous avez copiée à partir du portail Azure, dans la zone de texte **Logout URL** (URL de déconnexion).
    
    >[!NOTE]
    >Les valeurs d’URL de connexion et de déconnexion sont identiques pour Mimecast Admin Console.
    
    g. Dans le Bloc-notes, ouvrez le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure, supprimez la première ligne (« *--* ») et la dernière ligne (« *--* »), copiez le contenu restant dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)** (Certificat du fournisseur d’identité (Métadonnées)).
    
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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mimecast Admin Console.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Mimecast Admin Console**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Mimecast Admin Console**.

    ![Lien Mimecast Admin Console dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-mimecast-admin-console-test-user"></a>Créer un utilisateur de test Mimecast Admin Console

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Admin Console, vous devez les approvisionner dans Mimecast Admin Console. Dans le cas de Mimecast Admin Console, l’approvisionnement est une tâche manuelle.

* Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à **Mimecast Admin Console** en tant qu’administrateur.

2. Accédez à **Directories \> Internal**.
   
    ![Directories](./media/mimecast-admin-console-tutorial/ic795003.png "Répertoires")

3. Cliquez sur **Register New Domain**.
   
    ![Inscrire un nouveau domaine](./media/mimecast-admin-console-tutorial/ic795004.png "Register New Domain")

4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
    ![Nouvelle adresse](./media/mimecast-admin-console-tutorial/ic795005.png "New Address")

5. Dans la boîte de dialogue New Address, procédez comme suit :
   
    ![Save](./media/mimecast-admin-console-tutorial/ic795006.png "Enregistrer")
   
    a. Tapez l’adresse e-mail, le nom global, le mot de passe et sa confirmation pour un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **Global Name**, **Password** et **Confirm Password**.

    b. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fournis par Mimecast Admin Console, pour approvisionner des comptes d’utilisateur Azure AD. 

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Mimecast Admin Console dans le panneau d’accès, vous devez être connecté automatiquement à l’application Mimecast Admin Console pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

