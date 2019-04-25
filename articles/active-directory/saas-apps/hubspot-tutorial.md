---
title: 'Didacticiel : Intégration d’Azure Active Directory à HubSpot | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adcd0f094d584e770f1a3f4938ee677ba58a21a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995691"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Didacticiel : Intégration d’Azure AD à HubSpot

Dans ce didacticiel, vous allez apprendre à intégrer HubSpot dans Azure Active Directory (Azure AD).
L’intégration de HubSpot dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à HubSpot.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à HubSpot (avec l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec HubSpot, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement HubSpot pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* HubSpot prend en charge l’authentification unique initiée par le **fournisseur de services (SP) et le fournisseur d’identité (IDP)**

## <a name="adding-hubspot-from-the-gallery"></a>Ajout de HubSpot à partir de la galerie

Pour configurer l’intégration de HubSpot à Azure AD, vous devez ajouter HubSpot à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HubSpot à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **HubSpot**, sélectionnez **HubSpot** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![HubSpot dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec HubSpot sur un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur HubSpot associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec HubSpot, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique HubSpot](#configure-hubspot-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test HubSpot](#create-hubspot-test-user)** pour avoir un équivalent de Britta Simon dans HubSpot qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec HubSpot, effectuez les étapes suivantes :

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **HubSpot** , sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL HubSpot](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeur avec l’identificateur et l’URL de réponse réels. La procédure est expliquée plus loin dans le didacticiel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL HubSpot](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.hubspot.com/login`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer HubSpot**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-hubspot-single-sign-on"></a>Configurer l’authentification unique HubSpot

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur HubSpot.

2. Cliquez sur l’**icône de paramètres** en haut à droite dans la page.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config1.png)

3. Cliquez ensuite sur **Valeurs par défaut du compte**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config2.png)

4. Faites défiler jusqu’à la section **Sécurité** et cliquez sur **Configurer**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config3.png)

5. Dans la section **Set up single sign-on**, procédez comme suit :

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config4.png)

    a. Cliquez sur le bouton **Copier** pour copier l’**URI d’audience (ID entité fournisseur de services)** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** dans le portail Azure.

    b. Cliquez sur le bouton **Copier** pour copier la valeur **Sign on URl,ACS,Recipient ou Redirect** (URI de connexion, ACS, Destinataire ou Redirection) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure.

    c. Dans la zone de texte **Identity Provider Identifier or Issuer URL** (URL de l’identificateur ou l’émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.

    d. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Ouvrez votre  **Certificat (Base64)**  téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte  **Certificat X.509**.

    f. Cliquez sur **Vérifier**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HubSpot.

1. Sur le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **HubSpot**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **HubSpot**.

    ![Lien HubSpot dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-hubspot-test-user"></a>Créer un utilisateur de test HubSpot

Pour se connecter à HubSpot, les utilisateurs d’Azure AD doivent être configurés dans HubSpot. Dans le cas de HubSpot, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **HubSpot** en tant qu’administrateur.

2. Cliquez sur l’**icône de paramètres** en haut à droite dans la page.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config1.png)

3. Cliquez sur **Utilisateurs et équipes**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user1.png)

4. Cliquez sur **Create User** (Créer un utilisateur).

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user2.png)

5. Entrez l’adresse e-mail de l’utilisateur, par exemple `brittasimon\@contoso.com`, dans la zone de texte **Add email address(es)** (Ajouter une ou des adresses e-mail), puis cliquez sur **Suivant**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user3.png)

6. Dans la section **Créer des utilisateurs**, passez en revue chaque onglet individuel et sélectionnez les options et autorisations appropriées pour l’utilisateur, puis cliquez sur **Suivant**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user4.png)

7. Cliquez sur **Envoyer** pour envoyer l’invitation à l’utilisateur.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > L’utilisateur invité est activé après avoir accepté l’invitation.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette HubSpot dans le volet d’accès, vous devez être connecté automatiquement à l’application HubSpot pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

