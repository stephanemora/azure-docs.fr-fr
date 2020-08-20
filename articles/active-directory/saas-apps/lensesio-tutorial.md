---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Lenses.io | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528620"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory au portail DataOps Lenses.io | Microsoft Docs

Dans ce tutoriel, vous allez découvrir comment intégrer le portail DataOps [Lenses.io](https://lenses.io/) à Azure Active Directory (Azure AD). Quand vous intégrez Lenses.io à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès au portail Lenses.io.
* Permettre à vos utilisateurs de se connecter automatiquement à Lenses avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Une instance d’un portail Lenses. Vous pouvez déployer un portail Lenses de [différentes façons](https://lenses.io/product/deployment/)
* Une [licence](https://lenses.io/product/pricing/) Lenses.io qui prend en charge l’authentification unique

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Lenses.io prend en charge l’authentification unique lancée par le **fournisseur de services**

* Après avoir configuré Lenses.io, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Ajout de Lenses.io à partir de la galerie

Pour configurer l’intégration de Lenses.io à Azure AD, vous devez ajouter Lenses.io à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Lenses.io** dans la zone de recherche.
1. Sélectionnez **Lenses.io** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Lenses.io

Configurez et testez l’authentification unique Azure AD avec votre portail Lenses.io à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans Lenses.io.

Pour configurer et tester l’authentification unique Azure AD avec Lenses.io, effectuez les modules suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un groupe et un utilisateur de test Azure AD](#create-an-azure-ad-test-user-and-group)** pour tester l’authentification unique Azure AD avec B.Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Lenses.io](#configure-lensesio-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer des autorisations de groupe de test Lenses.io](#create-lensesio-test-group-permissions)** pour contrôler ce à quoi B.Simon doit accéder dans Lenses.io (autorisation).
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Lenses.io**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :  `https://<CUSTOMER_LENSES_BASE_URL>`, par exemple `https://lenses.my.company.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL au format suivant : `https://<CUSTOMER_LENSES_BASE_URL>`, par exemple `https://lenses.my.company.com`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    Exemple : `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec les valeurs réelles d’URL de connexion, d’URL de réponse et d’identificateur, en fonction de l’URL de base de votre instance de portail Lenses. Vous trouverez plus d’informations dans la [documentation sur l’authentification unique Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Lenses.io**, utilisez le fichier XML ci-dessus pour configurer Lenses par rapport à votre authentification unique Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Créer un groupe et un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure. Vous allez également créer un groupe de test pour B.Simon, qui servira à contrôler l’accès de B.Simon à Lenses.
Pour en savoir plus sur la façon dont Lenses utilise le mappage d’appartenance de groupe, consultez la [documentation sur l’authentification unique Lenses](https://docs.lenses.io/install_setup/configuration/security.html#id3).

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

Pour créer le groupe
1. Revenez à **Azure Active Directory** et sélectionnez **Groupes**.
1. Sélectionnez **Nouveau groupe** dans la partie supérieure de l’écran.
1. Dans les **Propriétés du groupe**, effectuez les étapes suivantes :
   1. Dans le champ **Type de groupe**, sélectionnez `Security`.
   1. Dans le champ **Nom du groupe**, entrez `LensesUsers`.
   1. Cliquez sur **Créer**.
1. Sélectionnez le groupe `LensesUsers` et prenez note de l’**ID d’objet** (par exemple `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`). Cet ID sera utilisé dans Lenses pour mapper les utilisateurs de ce groupe aux [autorisations appropriées](https://docs.lenses.io/install_setup/configuration/security.html#id3).  
   
Pour affecter le groupe à l’utilisateur de test 
1. Revenez à **Azure Active Directory** et sélectionnez **Utilisateurs**.
1. Sélectionnez l’utilisateur de test `B.Simon`.
1. Sélectionnez **Groupes**.
1. Sélectionnez **Ajouter des appartenances** en haut de l’écran.
1. Recherchez et sélectionnez `LensesUsers`.
1. Cliquez sur **Sélectionner**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lenses.io.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Lenses.io**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-lensesio-sso"></a>Configurer l’authentification unique Lenses.io

Pour configurer l’authentification unique dans le portail **Lenses.io**, vous devez installer le **XML des métadonnées de fédération** téléchargé sur votre instance de Lenses et [configurer Lenses pour activer l’authentification unique](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Créer des autorisations de groupe de test Lenses.io

Dans cette section, vous allez créer un groupe dans Lenses à l’aide de l’**ID d’objet** du groupe `LensesUsers` que nous avons noté dans la [section sur la création d’utilisateur](#create-an-azure-ad-test-user-and-group).
Vous allez attribuer les autorisations dont `B.Simon` doit disposer dans Lenses.
Pour plus d’informations sur le mappage de groupe Azure - Lenses, consultez [cet article](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Lenses.io dans le volet d’accès, vous devez être connecté automatiquement au portail Lenses.io pour lequel vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Configurer l’authentification unique dans votre instance de Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Lenses.io avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger Lenses.io avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
