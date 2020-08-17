---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec GitHub Enterprise Cloud – Enterprise Account | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub Enterprise Cloud – Enterprise Account.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 544b1e8c-f633-4d9b-92fe-077d4c51885a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c29de2d8150bce590d19f3af474dda87d17a47
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082056"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec GitHub Enterprise Cloud – Enterprise Account

Ce didacticiel explique comment intégrer le compte GitHub Enterprise Cloud-Enterprise avec Azure Active Directory (Azure AD). L’intégration de GitHub Enterprise Cloud – Enterprise Account avec Azure AD vous permet d’effectuer les opérations suivantes :

* Contrôler dans Azure AD qui a accès à GitHub Enterprise Cloud – Enterprise Account.
* Permettre que vos utilisateurs soient automatiquement connectés à GitHub Enterprise Cloud – Enterprise Account avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement GitHub Enterprise Cloud – Enterprise Account pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* GitHub Enterprise Cloud – Enterprise Account prend en charge l’authentification unique initiée par un **fournisseur de services** et un **fournisseur d’ID**.
* GitHub Enterprise Cloud – Enterprise Account prend en charge l’approvisionnement **juste-à-temps** d’utilisateurs.
* Une fois que vous avez configuré GitHub Enterprise Cloud – Enterprise Account, vous pouvez appliquer un contrôle de session qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Ajouter GitHub Enterprise Cloud – Enterprise Account à partir de la galerie

Pour configurer l’intégration de GitHub Enterprise Cloud – Enterprise Account dans Azure AD, vous devez ajouter GitHub Enterprise Cloud – Enterprise Account à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie** , tapez **GitHub Enterprise Cloud – Enterprise Account** dans la zone de recherche.
1. Sélectionnez **GitHub Enterprise Cloud – Enterprise Account** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Configurer et tester l’authentification unique Azure AD pour GitHub Enterprise Cloud – Enterprise Account

Configurez et testez l’authentification unique Azure AD avec GitHub Enterprise Cloud – Enterprise Account avec un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur associé dans GitHub Enterprise Cloud – Enterprise Account.

Pour configurer et tester l’authentification unique Azure AD avec GitHub Enterprise Cloud – Enterprise Account, vous devez accomplir les opérations suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique GitHub Enterprise Cloud – Enterprise Account](#configure-github-enterprise-cloud-enterprise-account-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test GitHub Enterprise Cloud – Enterprise Account](#create-github-enterprise-cloud-enterprise-account-test-user)** pour avoir un équivalent de B.Simon dans GitHub Enterprise Cloud – Enterprise Account lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **GitHub Enterprise Cloud – Enterprise Account**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

     Dans la zone de texte **URL de connexion**, entrez une URL au format suivant :  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’URL de réponse et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique GitHub Enterprise Cloud – Enterprise Account](mailto:support@github.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateBase64.png)

1. Dans la section **Configurer GitHub Enterprise Cloud – Enterprise Account**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès GitHub Enterprise Cloud – Enterprise Account.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **GitHub Enterprise Cloud – Enterprise Account**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Configurer l’authentification unique dans GitHub Enterprise Cloud – Enterprise Account

Pour configurer l’authentification unique côté **GitHub Enterprise Cloud – Enterprise Account**, vous devez envoyer le **Certificat (Base64)** téléchargé et les URL copiées correspondantes du portail Azure à l’[équipe du support technique GitHub Enterprise Cloud – Enterprise Account](mailto:support@github.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Créer un utilisateur de test GitHub Enterprise Cloud – Enterprise Account

Dans cette section, un utilisateur nommé B. Simon est créé dans GitHub Enterprise Cloud – Enterprise Account. GitHub Enterprise Cloud – Enterprise Account prend en charge l’approvisionnement juste-à-temps d’utilisateurs qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas encore dans GitHub Enterprise Cloud – Enterprise Account, un nouvel utilisateur est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette GitHub Enterprise Cloud – Enterprise Account dans le volet d’accès, vous devez être connecté automatiquement au GitHub Enterprise Cloud – Enterprise Account pour lequel vous configurez l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer GitHub Enterprise Cloud – Enterprise Account avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Comment protéger GitHub Enterprise Cloud – Enterprise Account avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)