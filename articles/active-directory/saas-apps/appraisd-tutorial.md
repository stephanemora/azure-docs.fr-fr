---
title: 'Tutoriel : Intégration d’Azure Active Directory à Appraisd | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8afd2201d55dfed94cf8f0dd34ca26870c0cc324
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169358"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Didacticiel : Intégration d’Azure Active Directory à Appraisd

Dans ce tutoriel, vous allez apprendre à intégrer Appraisd à Azure Active Directory (Azure AD).
L’intégration d’Appraisd à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Appraisd.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Appraisd (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Appraisd, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Appraisd pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Appraisd prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-appraisd-from-the-gallery"></a>Ajout d’Appraisd depuis la galerie

Pour configurer l’intégration d’Appraisd à Azure AD, vous devez ajouter Appraisd, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Appraisd à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Appraisd**, sélectionnez **Appraisd** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Appraisd dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Appraisd au moyen d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Appraisd associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Appraisd, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Appraisd](#configure-appraisd-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Appraisd](#create-appraisd-test-user)** pour avoir un équivalent de Britta Simon dans Appraisd, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Appraisd, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Appraisd**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Appraisd](common/both-preintegrated-advanced-urls.png)

    a. Cliquez sur **Définir des URL supplémentaires**.

    b. Dans la zone de texte **État de relais**, tapez une URL : `<TENANTCODE>`

    c. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de services**, dans la zone de texte **URL d’authentification**, tapez une URL au format suivant : `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Vous obtenez les valeurs d’URL de connexion et d’état de relais réelles dans la page de configuration de l’authentification unique pour Appraisd, comme expliqué plus loin dans le tutoriel.

5. L’application Appraisd attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom |  Attribut source|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

8. Dans la section **Configurer Appraisd**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-appraisd-single-sign-on"></a>Configurer l’authentification unique Appraisd

1. Dans une autre fenêtre de navigateur web, connectez-vous à Appraisd en tant qu’administrateur de la sécurité.

2. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à  **Configuration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. Sur le côté gauche du menu, cliquez sur **SAML single sign-on** (Authentification unique SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Dans la page **SAML 2.0 Single Sign-On configuration** (Configuration de l’authentification unique SAML 2.0), effectuez les étapes suivantes :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiez la valeur **Default Relay State** (État de relais par défaut) et collez-la dans la zone de texte  **État de relais** dans  **Configuration SAML de base** dans le portail Azure.

    b. Copiez la valeur **Service-initiated login URL** (URL de connexion lancée par le service) et collez-la dans la zone de texte  **État de relais** dans  **Configuration SAML de base** dans le portail Azure.

5. Faites défiler la même page vers le bas jusqu’à **Identifying users** (Identification des utilisateurs), puis effectuez les étapes suivantes :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    b. Dans la zone de texte **Identity Provider Issuer URL** (URL de l’émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure, puis cliquez sur **Save** (Enregistrer).

    c. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu, puis collez-le dans la zone  **X.509 Certificate** (Certificat X.509) et cliquez sur **Save** (Enregistrer).

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

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Appraisd.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Appraisd**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Appraisd**.

    ![Lien Appraisd dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-appraisd-test-user"></a>Créer un utilisateur de test Appraisd

Pour se connecter à Appraisd, les utilisateurs Azure AD doivent être attribués dans Appraisd. Dans Appraisd, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Appraisd en tant qu’administrateur de la sécurité.

2. Dans le coin supérieur droit de la page, cliquez sur l’icône **Paramètres**, puis accédez à  **Administration centre** (Centre d’administration).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Dans la barre d’outils en haut de la page, cliquez sur  **People** (Personnes), puis accédez à  **Add a new user** (Ajouter un nouvel utilisateur).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Dans la page **Add a new user** (Ajouter un nouvel utilisateur), effectuez les étapes suivantes :

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Dans la zone de texte **First name** (Prénom), tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    d. Cliquez sur **Add User**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Appraisd dans le volet d’accès, vous devez être connecté automatiquement à l’application Appraisd pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
