---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Elium | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "72791220"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Elium

Dans ce tutoriel, vous allez découvrir comment intégrer Elium à Azure Active Directory (Azure AD). Quand vous intégrez Elium à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Elium.
* Permettre à vos utilisateurs de se connecter automatiquement à Elium avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Elium pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Elium prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* Elium prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-elium-from-the-gallery"></a>Ajout d’Elium depuis la galerie

Pour configurer l’intégration d’Elium à Azure AD, vous devez ajouter Elium, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Elium** dans la zone de recherche.
1. Sélectionnez **Elium** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Configurer et tester l’authentification unique Azure AD pour Elium

Configurez et testez l’authentification unique Azure AD avec Elium à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Elium associé.

Pour configurer et tester l’authentification unique Azure AD avec Elium, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Elium](#configure-elium-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Elium](#create-elium-test-user)** pour avoir dans Elium un équivalent de B.Simon associé à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Elium**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/acs`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous obtenez ces valeurs à partir du **fichier de métadonnées SP** téléchargeable à l’adresse `https://<platform-domain>.elium.com/login/saml2/metadata`, procédure qui est expliquée plus loin dans ce tutoriel.

1. L’application Elium attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Elium s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | fonction| user.jobtitle|
    | société| user.companyname|

    > [!NOTE]
    > Ce sont les revendications par défaut. **Seule la revendication d’e-mail est obligatoire**. Pour le provisionnement JIT, cette revendication est également la seule obligatoire. D’autres revendications personnalisées peuvent varier d’une plateforme de client à l’autre.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Elium**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Elium.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Elium**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-elium-sso"></a>Configurer l’authentification unique Elium

1. Pour automatiser la configuration dans Elium, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Elium** pour être dirigé vers l’application Elium. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Elium. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Elium, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Elium en tant qu’administrateur et effectuez les étapes suivantes :

1. Cliquez sur le **Profil utilisateur** dans le coin supérieur droit, puis sélectionnez **Administration**.

    ![Configure Single Sign-On](./media/elium-tutorial/user1.png)

1. Sélectionnez l’onglet **Sécurité**.

    ![Configure Single Sign-On](./media/elium-tutorial/user2.png)

1. Faites défiler jusqu’à la section **Single Sign-On SSO** (Authentification unique) et effectuez les étapes suivantes :

    ![Configure Single Sign-On](./media/elium-tutorial/user3.png)

    a. Copiez la valeur de **Verify that SAML2 authentication works for your account** (Vérifiez que l’authentification SAML2 fonctionne pour votre compte) et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    > [!NOTE]
    > Après avoir configuré l’authentification unique, vous conservez l’accès à la page de connexion à distance par défaut en utilisant l’URL suivante : `https://<platform_domain>/login/regular/login` 

    b. Sélectionnez la case à cocher **Enable SAML2 federation** (Activer la fédération SAML2).

    c. Sélectionnez la case à cocher **JIT Provisioning** (Provisionnement JIT).

    d. Ouvrez **SP Metadata** (Métadonnées SP) en cliquant sur le bouton **Download** (Télécharger).

    e. Recherchez l’**entityID** (identificateur d’utilisateur) dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **entityID**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure. 

    ![Configure Single Sign-On](./media/elium-tutorial/user4.png)

    f. Recherchez **AssertionConsumerService** dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **Location**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    ![Configure Single Sign-On](./media/elium-tutorial/user5.png)

    g. Dans le Bloc-notes, ouvrez le fichier de métadonnées téléchargé depuis le portail Azure, copiez son contenu et collez-le dans la zone de texte **IdP Metadata** (Métadonnées du fournisseur d’identité).

    h. Cliquez sur **Enregistrer**.

### <a name="create-elium-test-user"></a>Créer un utilisateur de test Elium

Dans cette section, un utilisateur appelé B.Simon est créé dans Elium. Elium prend en charge l’**attribution d’utilisateurs juste-à-temps**, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas d’utilisateur dans Elium, un nouvel utilisateur est créé lorsque vous tentez d’y accéder.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Elium dans le volet d’accès, vous devez être connecté automatiquement à l’application Elium pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Elium avec Azure AD](https://aad.portal.azure.com/)