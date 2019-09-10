---
title: 'Didacticiel : Intégration de l’authentification unique (SSO) Azure Active Directory à Jamf Pro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305434"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Didacticiel : Intégration de l’authentification unique (SSO) Azure Active Directory à Jamf Pro

Dans ce tutoriel, vous allez apprendre à intégrer Jamf Pro dans Azure Active Directory (Azure AD). Quand vous intégrez Jamf Pro à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Jamf Pro.
* Permettre à vos utilisateurs de se connecter automatiquement à Jamf Pro avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Jamf Pro pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Jamf Pro prend en charge l’authentification unique initiée par **SP et IDP**

## <a name="adding-jamf-pro-from-the-gallery"></a>Ajout de Jamf Pro à partir de la galerie

Pour configurer l’intégration de Jamf Pro à Azure AD, vous devez ajouter Jamf Pro à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Jamf Pro** dans la zone de recherche.
1. Sélectionnez **Jamf Pro** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Configurer et tester l’authentification unique Azure AD pour Jamf Pro

Configurez et testez l’authentification unique Azure AD avec Jamf Pro à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Jamf Pro associé.

Pour configurer et tester l’authentification unique Azure AD avec Jamf Pro, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Jamf Pro](#configure-jamf-pro-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Jamf Pro](#create-jamf-pro-test-user)** pour avoir dans Jamf Pro un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Jamf Pro**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez la valeur avec l’identificateur réel à partir de la section **Authentification unique** dans le portail Jamf Pro. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez extraire la valeur de **sous-domaine** réelle à partir de la valeur d’identificateur et utiliser ces informations de **sous-domaine** dans l’URL de connexion et l’URL de réponse. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jamf Pro.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Jamf Pro**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-jamf-pro-sso"></a>Configurer l’authentification unique Jamf Pro

1. Pour automatiser la configuration dans Jamf Pro, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Jamf Pro**, vous êtes alors orienté vers l’application Jamf Pro. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Jamf Pro. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Jamf Pro, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur et effectuez les étapes suivantes :

4. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Cliquez sur **Authentification unique**.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Sur la page **Authentification unique**, effectuez les étapes suivantes :

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Cochez l’option **Enable Single Sign-On Authentication** (Activer l’authentification unique).

    b. Sélectionnez **Autres** comme option dans le menu déroulant **FOURNISSEUR D’IDENTITÉ**.

    c. Dans la zone de texte **OTHER PROVIDER (Autre fournisseur)** , entrez **Azure AD**.

    d. Copiez la valeur **ENTITY ID** (ID d’entité) et collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Configuration SAML de base** du portail Azure.

    > [!NOTE]
    > Ici figure la partie `<SUBDOMAIN>`. Vous devez utiliser cette valeur pour renseigner l’URL de connexion et l’URL de réponse dans la section **Configuration SAML de base** du portail Azure.

    e. Sélectionnez **Metadata URL** (URL des métadonnées) comme option dans la liste déroulante **IDENTITY PROVIDER METADATA SOURCE** (Source des métadonnées du fournisseur d’identité), puis dans la zone de texte suivante, collez la valeur d’**URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

7. Dans la même page, faites défiler jusqu’à la section **User Mapping** (Mappage d’utilisateurs) et effectuez les étapes suivantes : 

    ![Authentification unique Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Sélectionnez l’option **NameID** pour **IDENTITY PROVIDER USER MAPPING** (MAPPAGE D’UTILISATEURS DE FOURNISSEUR D’IDENTITÉ). Par défaut, ce paramètre est défini sur **NameID**, mais vous pouvez définir un attribut personnalisé.

    b. Sélectionnez **Email** pour **JAMF PRO USER MAPPING** (MAPPAGE D’UTILISATEURS JAMF PRO). Jamf Pro mappe les attributs SAML envoyés par le fournisseur d’identité de différentes manières : par utilisateurs et par groupes. Lorsqu’un utilisateur tente d’accéder à Jamf Pro, Jamf Pro obtient par défaut des informations sur l’utilisateur de la part du fournisseur d’identité, et les compare avec les comptes d’utilisateur Jamf Pro. Si le compte d’utilisateur entrant n’existe pas dans Jamf Pro, alors une correspondance de nom du groupe se produit.

    c. Collez la valeur `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` dans la zone de texte **IDENTITY PROVIDER GROUP ATTRIBUTE NAME** (NOM DE L’ATTRIBUT DE GROUPE DE FOURNISSEUR D’IDENTITÉ).

    d. Si vous sélectionnez **Allow users to bypass the Single Sign-On authentication** (Autoriser les utilisateurs à contourner l’authentification unique), les utilisateurs ne seront pas redirigés vers la page de connexion du fournisseur d’identité pour l’authentification, mais pourront se connecter à Jamf Pro directement. Lorsqu’un utilisateur tente d’accéder à Jamf Pro via le fournisseur d’identité, l’autorisation et l’authentification SSO lancée par le fournisseur d’identité se produisent.

    e. Cliquez sur **Enregistrer**.

### <a name="create-jamf-pro-test-user"></a>Créer un utilisateur de test Jamf Pro

Pour pouvoir se connecter à Jamf Pro, les utilisateurs d’Azure AD doivent être provisionnés dans Jamf Pro. Dans le cas de Jamf Pro, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

2. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Cliquez sur **Jamf Pro User Accounts & Groups (Groupes et comptes d’utilisateur Jamf Pro)** .

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user1.png)

4. Cliquez sur **Nouveau**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user2.png)

5. Sélectionnez **Create Standard Account (Créer un compte standard)** .

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user3.png)

6. Dans la boîte de dialogue **New Account (Nouveau compte)** , procédez comme suit :

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Dans la zone de texte **USERNAME (Nom d’utilisateur)** , tapez le nom complet BrittaSimon.

    b. Sélectionnez les options adaptées à votre organisation pour **ACCESS LEVEL** (Niveau d’accès), **PRIVILEGE SET (Ensemble de privilèges)** et **ACCESS STATUS (État de l’accès)** .

    c. Dans la zone de texte **FULL NAME (Nom complet)** , tapez Britta Simon.

    d. Dans la zone de texte **EMAIL ADDRESS (Adresse e-mail)** , tapez l’adresse e-mail du compte de Britta Simon.

    e. Dans la zone de texte **PASSWORD (Mot de passe)** , tapez un mot de passe pour l’utilisateur.

    f. Dans la zone de texte **VERIFY PASSWORD (Vérifier le mot de passe)** , tapez le mot de passe de l’utilisateur.

    g. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Jamf Pro dans le panneau d’accès doit vous connecter automatiquement à l’application Jamf Pro pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Jamf Pro avec Azure AD](https://aad.portal.azure.com/)

