---
title: 'Didacticiel : Intégration d’Azure Active Directory à AlertOps | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: jeedes
ms.openlocfilehash: 15f362386197c76de578e2ffffae4c165ba59c67
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568856"
---
# <a name="tutorial-azure-active-directory-integration-with-alertops"></a>Didacticiel : Intégration d’Azure Active Directory à AlertOps

Dans ce tutoriel, vous allez découvrir comment intégrer AlertOps à Azure Active Directory (Azure AD).
L’intégration d’AlertOps à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à AlertOps.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à AlertOps (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à AlertOps, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement AlertOps pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AlertOps prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-alertops-from-the-gallery"></a>Ajout d’AlertOps à partir de la galerie

Pour configurer l’intégration d’AlertOps à Azure AD, vous devez ajouter AlertOps à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter AlertOps à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **AlertOps**, sélectionnez **AlertOps** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![AlertOps dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AlertOps avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur AlertOps associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AlertOps, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique AlertOps](#configure-alertops-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test AlertOps](#create-alertops-test-user)** pour avoir un équivalent de Britta Simon dans AlertOps lié à la représentation de l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec AlertOps, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **AlertOps**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL AlertOps](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.alertops.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.alertops.com/login.aspx`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL AlertOps](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support client AlertOps](mailto:support@alertops.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer AlertOps**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-alertops-single-sign-on"></a>Configurer l’authentification unique AlertOps

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise AlertOps en tant qu’administrateur.

2. Sélectionnez **Account Settings** (Paramètres du compte) dans le volet de navigation gauche.

    ![Configuration d’AlertOps](./media/alertops-tutorial/configure1.png)

3. Dans la page **Subscription Settings** (Paramètres d’abonnement) , sélectionnez **SSO** et effectuez les étapes suivantes :

    ![Configuration d’AlertOps](./media/alertops-tutorial/configure2.png)

    a. Cochez la case **Use Single Sign-On(SSO)** (Utiliser l’authentification unique).

    b. Sélectionnez **Azure Active Directory** comme **SSO Provider** (Fournisseur d’authentification unique) dans la liste déroulante.

    c. Dans la zone de texte **Issuer URL** (URL de l’émetteur), utilisez la même valeur d’identificateur que celle de la section **Configuration SAML de base** du portail Azure.

    d. Dans la zone de texte **SAML endpoint URL** (URL de point de terminaison SAML), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **SLO endpoint URL** (URL de point de terminaison SLO), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    f. Sélectionnez **SHA256** comme **SAML Signature Algorithm** (Algorithme de Signature SAML) dans la liste déroulante.

    g. Ouvrez votre Certificat (Base64) téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte X.509 Certificate (Certificat X.509).

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AlertOps.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **AlertOps**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **AlertOps**.

    ![Lien AlertOps dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-alertops-test-user"></a>Créer un utilisateur de test AlertOps

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise AlertOps en tant qu’administrateur.

2. Cliquez sur **Users** (Utilisateurs) dans le panneau de navigation gauche.

    ![Configuration d’AlertOps](./media/alertops-tutorial/user1.png)

3. Sélectionnez **Add User** (Ajouter un utilisateur).

    ![Configuration d’AlertOps](./media/alertops-tutorial/user2.png)

4. Dans la boîte de dialogue **Add User** (Ajouter un utilisateur), procédez comme suit :

    ![Configuration d’AlertOps](./media/alertops-tutorial/user3.png)

    a. Dans la zone de texte **Login User Name** (Nom d’utilisateur de connexion), entrez le nom d’un utilisateur, par exemple **Brittasimon**.

    b. Dans la zone de texte **Official Email** (E-mail officiel), entrez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    c. Dans la zone de texte **First Name** (Prénom), entrez le prénom de l’utilisateur, par exemple **Britta**.

    d. Dans la zone de texte **Last name** (Nom), tapez le nom de famille de l’utilisateur, par exemple **Simon**.

    e. Sélectionnez la valeur **Type** dans la liste déroulante en fonction de votre organisation.

    f. Sélectionnez le **Rôle** de l’utilisateur dans la liste déroulante en fonction de votre organisation.

    g. Sélectionnez **Ajouter**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette AlertOps dans le volet d’accès, vous devez être connecté automatiquement à l’application AlertOps pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

