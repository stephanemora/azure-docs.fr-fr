---
title: "Didacticiel : Intégration d'Azure Active Directory à Velpic SAML | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 883620d6aa25f71a454c7add6d60e38b4974facf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087664"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Didacticiel : Intégration d'Azure Active Directory à Velpic SAML

Dans ce didacticiel, vous allez apprendre à intégrer Velpic SAML à Azure Active Directory (Azure AD).
L’intégration de Velpic SAML à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Velpic SAML.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Velpic SAML (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Velpic SAML, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement Velpic SAML pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Velpic SAML prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-velpic-saml-from-the-gallery"></a>Ajout de Velpic SAML à partir de la galerie

Pour configurer l’intégration de Velpic SAML à Azure AD, vous devez ajouter Velpic SAML à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Velpic SAML à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Velpic SAML**, sélectionnez **Velpic SAML** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Velpic SAML dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Velpic SAML à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Velpic SAML associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Velpic SAML, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Velpic SAML](#configure-velpic-saml-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Velpic SAML](#create-velpic-saml-test-user)** pour avoir un équivalent de Britta Simon dans Velpic SAML lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Velpic SAML, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Velpic SAML**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Velpic SAML](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<sub-domain>.velpicsaml.net`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Notez que l’URL d’authentification unique sera fournie par l’équipe Velpic SAML et la valeur Identificateur sera disponible lorsque vous configurez le plug-in SSO du côté Velpic SAML. Vous devez copier cette valeur à partir de la page d’application Velpic SAML et la coller ici.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Velpic SAML**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-velpic-saml-single-sign-on"></a>Configurer l’authentification unique Velpic SAML

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur.

2. Cliquez sur l’onglet **Gérer** et accédez à la section **Intégration** dans laquelle vous devez cliquer sur **Plug-ins** afin de créer le nouveau plug-in pour la connexion.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_1.png)

3. Cliquez sur le bouton **Ajouter un plug-in**.
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_2.png)

4. Cliquez sur la mosaïque **SAML** dans la page Ajouter un plug-in.
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_3.png)

5. Entrez le nom du nouveau plug-in SAML puis cliquez sur le bouton **Ajouter**.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_4.png)

6. Entrez les informations comme suit :

    ![Plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. Dans la zone de texte **Nom**, saisissez le nom du plug-in SAML.

    b. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez l’**Identificateur Azure AD** que vous avez copié à partir de la fenêtre **Configurer l’authentification** du portail Azure.

    c. Dans la section **Configuration des métadonnées du fournisseur**, téléchargez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    d. Vous pouvez également choisir d’activer l’approvisionnement immédiat SAML en cochant la case **Créer automatiquement les nouveaux utilisateurs**. Si un utilisateur n’existe pas dans Velpic et que cet indicateur n’est pas activé, la connexion à partir d’Azure échouera. Si l’indicateur est activé, l’utilisateur sera automatiquement approvisionné dans Velpic au moment de la connexion. 

    e. Copiez l **’URL d’authentification unique** à partir de la zone de texte et collez-la dans le portail Azure.
    
    f. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Velpic SAML.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Velpic SAML**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Velpic SAML**.

    ![Lien Velpic SAML dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-velpic-saml-test-user"></a>Créer un utilisateur de test Velpic SAML

Cette étape n’est généralement pas nécessaire car l’application prend en charge l’approvisionnement immédiat de l’utilisateur. Si l’approvisionnement automatique de l’utilisateur n’est pas activé, la création manuelle de l’utilisateur peut être effectuée comme décrit ci-dessous.

Connectez-vous au site de votre entreprise Velpic SAML en tant qu’administrateur et effectuez les étapes suivantes :
    
1. Cliquez sur l’onglet Gérer et accédez à la section Utilisateurs, puis cliquez sur le bouton Nouveau pour ajouter des utilisateurs.

    ![ajouter un utilisateur](./media/velpicsaml-tutorial/velpic_7.png)

2. Dans la boîte de dialogue **Create New User**, procédez comme suit.

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Dans la zone de texte **First Name** (Prénom), tapez Britta.

    b. Dans la zone de texte **Last Name** (Nom), tapez Simon.

    c. Dans la zone de texte **User Name**, tapez le nom d’utilisateur de Britta Simon.

    d. Dans la zone de texte **Email**, tapez l’adresse e-mail du compte Brittasimon@contoso.com.

    e. Les autres informations sont facultatives et vous pouvez les renseigner si nécessaire.
    
    f. Cliquez sur **ENREGISTRER**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

1. Lorsque vous cliquez sur la mosaïque Velpic SAML dans le volet d’accès, la page de connexion de l’application Velpic SAML devrait apparaître. Vous devriez voir le bouton **Se connecter à Azure AD** sur la page de connexion.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Cliquez sur le bouton **Se connecter à Azure AD** pour vous connecter à Velpic à l’aide de votre compte Azure AD.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

