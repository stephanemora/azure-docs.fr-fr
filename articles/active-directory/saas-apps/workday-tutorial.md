---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Workday | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527171"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Workday

Dans ce tutoriel, vous allez apprendre à intégrer Workday à Azure Active Directory (Azure AD). Quand vous intégrez Workday à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Workday.
* Permettre à vos utilisateurs de se connecter automatiquement à Workday avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Workday pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Workday prend en charge l’authentification unique lancée par le **fournisseur de services**.

* Après avoir configuré Workday, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrir comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Ajout de Workday à partir de la galerie

Pour configurer l’intégration de Workday à Azure AD, vous devez ajouter Workday à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Workday** dans la zone de recherche.
1. Sélectionnez **Workday** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Configurer et tester l’authentification unique Azure AD pour Workday

Configurez et testez l’authentification unique (SSO) Azure AD avec Workday à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Workday associé.

Pour configurer et tester l’authentification unique Azure AD avec Workday, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer Workday](#configure-workday)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Workday](#create-workday-test-user)** pour disposer, dans Workday, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Workday**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la boîte de dialogue **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://impl.workday.com/<tenant>/login-saml2.flex`.

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `http://www.workday.com`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Votre URL de réponse doit avoir un sous-domaine (par exemple, www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > `http://www.myworkday.com` fonctionne à la différence de `http://myworkday.com`. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Workday](https://www.workday.com/en-us/partners-services/services/support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Votre application Workday attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application Workday s’attend à ce que **nameidentifier** soit mappé avec **user.mail**, **UPN**, etc. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Ici, nous avons mappé l’ID de nom sur le nom d’utilisateur principal (user.userprincipalname) par défaut. Pour que l’authentification unique fonctionne correctement, vous devez mapper l’ID de nom sur l’identificateur d’utilisateur réel de votre compte Workday (votre adresse e-mail, votre nom d’utilisateur principal, etc.).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Pour modifier les options **Signature** selon vos besoins, cliquez sur le bouton **Modifier** afin d’ouvrir la boîte de dialogue **Certificat de signature SAML**.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Sélectionnez **Signer la réponse et l’assertion SAML** comme **Option de signature**.

    b. Cliquez sur **Enregistrer**.

1. Dans la section **Configurer Workday**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workday.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Workday**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-workday"></a>Configurer Workday

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Workday en tant qu’administrateur.

2. Dans la **zone de recherche**, recherchez le nom **Edit Tenant Setup – Security** en haut à gauche de la page d’accueil.

    ![Modifier la sécurité du locataire](./media/workday-tutorial/IC782925.png "Modifier la sécurité du locataire")

3. Dans la section **Redirection URLs** , procédez comme suit :

    ![URL de redirection](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. Cliquez sur le **signe plus**pour ajouter une ligne.

    b. Dans les zones de texte **Login Redirect URL** (URL de redirection de connexion), **Timeout Redirect URL** (URL de redirection de déconnexion) et **Mobile Redirect URL** (URL de redirection mobile), collez l’**URL de connexion** que vous avez copiée à partir de la section **Configurer Workday** du portail Azure.

    c. Dans la zone de texte **Logout Redirect URL** (URL de redirection de déconnexion), collez l’**URL de connexion** que vous avez copiée à partir de la section **Configurer Workday** du portail Azure.

    d. Dans la zone de texte **Used for Environments** (Utilisé pour les environnements), sélectionnez le nom de l’environnement.  

   > [!NOTE]
   > La valeur de l’attribut Environment est liée à celle de l’URL du client :  
   > \- Si le nom de domaine de l’URL du locataire Workday commence par impl (par exemple : *https://www.myworkday.com/"tenant"/login-saml2.htmld*), l’attribut **Environment** (Environnement) doit être défini sur Implementation (Implémentation).  
   > \- Si le nom de domaine commence par autre chose, vous devez contacter l’[équipe de support technique de Workday](https://www.workday.com/en-us/partners-services/services/support.html) pour obtenir la valeur correspondante de l’attribut **Environment**.

4. Dans la section **SAML Setup** , procédez comme suit :

    ![Configuration SAML](./media/workday-tutorial/IC782926.png "SAML Setup")

    a.  Sélectionnez **Enable SAML Authentication**.

    b.  Cliquez sur le **signe plus**pour ajouter une ligne.

5. Dans la section **SAML Identity Providers** (Fournisseurs d’identité SAML), effectuez les étapes suivantes :

    ![Fournisseurs d’identité SAML](./media/workday-tutorial/IC7829271.png "Fournisseurs d’identité SAML")

    a. Dans la zone de texte **Identity Provider Name** (Nom du fournisseur d’identité), entrez le nom d’un fournisseur (par exemple : *SPInitiatedSSO*).

    b. Dans le portail Azure, dans la fenêtre **Set up Workday** (Configurer Workday), copiez la valeur de **Identificateur Azure AD**, puis collez-la dans la zone de texte **Émetteur**.

    ![Fournisseurs d’identité SAML](./media/workday-tutorial/IC7829272.png "Fournisseurs d’identité SAML")

    c. Dans le portail Azure, dans la fenêtre **Set up Workday** (Configurer Workday), copiez l’**URL de déconnexion**, puis collez-la dans la zone de texte **Logout Response URL** (URL de réponse de déconnexion).

    d. Dans le portail Azure, dans la fenêtre **Set up Workday** (Configurer Workday), copiez l’**URL de connexion**, puis collez-la dans la zone de texte **IdP SSO Service URL** (URL du service d’authentification unique du fournisseur d'identité).

    e. Dans la zone de texte **Used for Environments** (Utilisé pour les environnements), sélectionnez le nom de l’environnement.

    f. Cliquez sur **Certificat de clé publique du fournisseur d’identité**, puis sur **Créer**.

    ![Créer](./media/workday-tutorial/IC782928.png "Créer")

    g. Cliquez sur **Créer la clé publique x509**.

    ![Créer](./media/workday-tutorial/IC782929.png "Créer")

6. Dans la section **Afficher la clé publique x509** , procédez comme suit :

    ![Afficher la clé publique x509](./media/workday-tutorial/IC782930.png "Afficher la clé publique x509")

    a. Dans la zone de texte **Name**, tapez le nom du certificat (par exemple : *PPE\_SP*).

    b. Dans la zone de texte **Valid From** , tapez la valeur correspondante du certificat.

    c.  Dans la zone de texte **Valid To** , tapez la valeur correspondante du certificat.

    > [!NOTE]
    > Vous pouvez obtenir les dates de début et de fin de validité du certificat téléchargé en double-cliquant dessus.  Les dates sont répertoriées sous l’onglet **Details** .
    >
    >

    d.  Ouvrez le certificat codé en base 64 dans le Bloc-notes, puis copiez son contenu.

    e.  Dans la zone de texte **Certificat** , collez le contenu du Presse-papiers.

    f.  Cliquez sur **OK**.

7. Procédez comme suit :

    ![Configuration de l’authentification unique](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuration SSO")

    a.  Dans la zone de texte **ID fournisseur de services**, tapez **http://www.workday.com** .

    b. Sélectionnez **Ne pas compresser la demande d’authentification initiée par le fournisseur de services**.

    c. Comme **Méthode de signature de la demande d’authentification**, sélectionnez **SHA256**.

    ![Méthode de signature de la demande d’authentification](./media/workday-tutorial/WorkdaySSOConfiguration.png "Méthode de signature de la demande d’authentification")

    d. Cliquez sur **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Vérifiez que vous avez correctement configuré l’authentification unique. Si vous activez l’authentification unique avec une configuration incorrecte, vous risquez de ne pas pouvoir accéder à l’application avec vos informations d’identification et de vous retrouver verrouillé. Dans ce type de situation, Workday fournit une URL de connexion de secours permettant aux utilisateurs de se connecter en utilisant leurs nom d’utilisateur et mot de passe standard au format suivant : [votre URL Workday]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Créer un utilisateur de test Workday

Dans cette section, vous créez un utilisateur appelé B.Simon dans Workday. Collaborez avec [l’équipe du support technique Workday](https://www.workday.com/en-us/partners-services/services/support.html) pour ajouter des utilisateurs sur la plateforme Workday. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette Workday dans le panneau d’accès, vous devez être connecté automatiquement à l’application Workday pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Workday avec Azure AD](https://aad.portal.azure.com)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger Workday avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/protect-workday)