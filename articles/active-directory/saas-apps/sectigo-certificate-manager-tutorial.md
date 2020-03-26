---
title: "Tutoriel : Intégration d'Azure Active Directory à Sectigo Certificate Manager | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588233"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutoriel : Intégration d'Azure Active Directory à Sectigo Certificate Manager

L’objectif de ce tutoriel est de vous apprendre à intégrer Sectigo Certificate Manager à Azure Active Directory (Azure AD).

L’intégration de Sectigo Certificate Manager à Azure AD vous offre les avantages suivants :

* Vous pouvez utiliser Azure AD pour contrôler qui a accès à Sectigo Certificate Manager.
* Les utilisateurs peuvent être automatiquement connectés à Sectigo Certificate Manager avec leur compte Azure AD (par le biais de l’authentification unique).
* Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications software as a service (SaaS) à Azure AD, consultez l’article [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sectigo Certificate Manager, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’abonnement Azure AD, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Abonnement à Sectigo Certificate Manager pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test et intégrer Sectigo Certificate Manager à Azure AD.

Sectigo Certificate Manager prend en charge les fonctionnalités suivantes :

* **Authentification unique lancée par le fournisseur de services**
* **Authentification unique démarrée par le fournisseur d’identité**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Ajouter Sectigo Certificate Manager dans le portail Azure

Pour intégrer Sectigo Certificate Manager à Azure AD, vous devez ajouter Sectigo Certificate Manager à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

    ![Option Azure Active Directory](common/select-azuread.png)

1. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Pour ajouter une application, sélectionnez **Nouvelle application**.

    ![Option Nouvelle application](common/add-new-app.png)

1. Dans la zone de recherche, entrez **Sectigo Certificate Manager**. Dans les résultats de la recherche, sélectionnez **Sectigo Certificate Manager**, puis **Ajouter**.

    ![Sectigo Certificate Manager dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Sectigo Certificate Manager via un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur Sectigo Certificate Manager.

Pour configurer et tester l’authentification unique d’Azure AD avec Sectigo Certificate Manager, vous devez suivre les indications des sections suivantes :

| Tâche | Description |
| --- | --- |
| **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** | Autorise les utilisateurs à utiliser cette fonctionnalité. |
| **[Configurer l’authentification unique de Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Configure les paramètres d’authentification unique dans l’application. |
| **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** | Teste l’authentification unique Azure AD pour un utilisateur appelé Britta Simon. |
| **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** | Active Britta Simon pour lui permettre d'utiliser l’authentification unique Azure AD. |
| **[Créer utilisateur de test Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Crée un équivalent de Britta Simon dans Sectigo Certificate Manager lié à la représentation d’un utilisateur Azure AD. |
| **[Tester l’authentification unique](#test-single-sign-on)** | Vérifie que la configuration fonctionne. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous configurez l’authentification unique Azure AD avec Sectigo Certificate Manager dans le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), à la page d’intégration de l’application **Sectigo Certificate Manager**, sélectionnez **Authentification unique**.

    ![Configurer l’option d’authentification unique](common/select-sso.png)

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** ou **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier** (icône de crayon) pour ouvrir le volet **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans le volet **Configuration SAML de base**, pour configurer le *mode initié par le fournisseur d’identité*, effectuez les étapes suivantes :

    1. Dans la zone **Identificateur**, entrez une de ces URL :
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Dans la zone **URL de réponse**, entrez une de ces URL :
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Sélectionnez **Définir des URL supplémentaires**.

    1. Dans la zone **État de relais**, entrez une de ces URL :
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Informations d’authentification unique pour le domaine et les URL de Sectigo Certificate Manager](common/idp-relay.png)

1.  Pour configurer l’application en mode lancé par le *fournisseur de services*, procédez comme suit :

    * Dans la zone **URL de connexion**, entrez une de ces URL :
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Informations d’authentification unique pour le domaine et les URL de Sectigo Certificate Manager](common/both-signonurl.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sous la section **Certificat de signature SAML**, sélectionnez **Télécharger** en regard de **Certificat (Base64)** . Sélectionnez une option de téléchargement en fonction de vos exigences. Enregistrez le certificat sur votre ordinateur.

    ![Option de téléchargement du certificat (Base64)](common/certificatebase64.png)

1. Dans la section **Configurer Sectigo Certificate Manager**, copiez les URL appropriées suivantes en fonction de vos besoins :

    * URL de connexion
    * Identificateur Azure AD
    * URL de déconnexion

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurer l’authentification unique de Sectigo Certificate Manager

Pour configurer l’authentification unique côté Sectigo Certificate Manager, envoyez le fichier de certificat (Base64) téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support technique Sectigo Certificate Manager](https://sectigo.com/support). L’équipe du support technique Sectigo Certificate Manager utilise les informations que vous lui envoyez pour vérifier que la connexion SAML à authentification unique est correctement configurée des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

Dans cette section, créez un utilisateur de test nommé Britta Simon dans le portail Azure.

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

    ![Options Utilisateurs et Tous les utilisateurs](common/users.png)

1. Sélectionnez **Nouvel utilisateur**.

    ![Option Nouvel utilisateur](common/new-user.png)

1. Dans le volet **Utilisateur**, effectuez les étapes suivantes :

    1. Dans la zone **Nom**, entrez **BrittaSimon**.
  
    1. Dans la zone **Nom d’utilisateur**, entrez **brittasimon\@\<votre_domaine_d’entreprise>.\<extension>\>** . Par exemple, **brittasimon\@contoso.com**.

    1. Cochez la case **Afficher le mot de passe**. Notez la valeur affichée dans la zone **Mot de passe**.

    1. Sélectionnez **Create** (Créer).

    ![Volet Utilisateur](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à accéder à Sectigo Certificate Manager pour lui permettre d'utiliser l'authentification unique Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications** > **Sectigo Certificate Manager**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager dans la liste des applications](common/all-applications.png)

1. Dans le menu, sélectionnez **Utilisateurs et groupes**.

    ![Option Utilisateurs et groupes](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

1. Dans le volet **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs. Choisissez **Select**.

1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans le volet **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Select**.

1. Dans le volet **Ajouter une attribution**, sélectionnez **Attribuer**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Créer utilisateur de test Sectigo Certificate Manager

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Sectigo Certificate Manager. Collaborez avec [l’équipe du support technique Sectigo Certificate Manager](https://sectigo.com/support) pour ajouter l'utilisateur à la plateforme Sectigo Certificate Manager. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à partir du portail Mes applications.

Après avoir configuré l’authentification unique, lorsque vous sélectionnez **Sectigo Certificate Manager** dans le portail Mes applications, vous êtes automatiquement connecté à Sectigo Certificate Manager. Pour plus d’informations sur le portail Mes applications, consultez [Accéder aux applications du portail Mes applications et les utiliser](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Listes des tutoriels pour intégrer des applications SaaS à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


