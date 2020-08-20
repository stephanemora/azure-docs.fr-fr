---
title: 'Didacticiel : Intégration d’Azure Active Directory à TigerText Secure Messenger | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TigerText Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516977"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Didacticiel : Intégration d’Azure Active Directory à TigerText Secure Messenger

Dans ce didacticiel, vous allez apprendre à intégrer TigerText Secure Messenger à Azure Active Directory (Azure AD).

L’intégration de TigerText Secure Messenger dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à TigerText Secure Messenger.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à TigerText Secure Messenger (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour obtenir des détails sur l’intégration d’applications SaaS (software as a service) avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à TigerText Secure Messenger, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un abonnement TigerText Secure Messenger pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test et intégrer TigerText Secure Messenger à Azure AD.

TigerText Secure Messenger prend en charge l’authentification unique lancée par le fournisseur de services.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Ajouter TigerText Secure Messenger à partir de la Place de marché Azure

Pour configurer l’intégration de TigerText Secure Messenger à Azure AD, vous devez ajouter TigerText Secure Messenger, disponible dans la Place de marché Azure, à votre liste d’applications SaaS managées :

1. Connectez-vous au [portail Azure](https://portal.azure.com?azure-portal=true).
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.

    ![Option Azure Active Directory](common/select-azuread.png)

1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Pour ajouter une nouvelle application, sélectionnez **+ Nouvelle application** dans la partie supérieure du volet.

    ![Option Nouvelle application](common/add-new-app.png)

1. Dans la zone de recherche, entrez **TigerText Secure Messenger**. Dans les résultats de recherche, sélectionnez **TigerText Secure Messenger**, puis **Ajouter** pour ajouter l’application.

    ![TigerText Secure Messenger dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de TigerText Secure Messenger pour un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TigerText Secure Messenger associé.

Pour configurer et tester l’authentification unique Azure AD avec TigerText Secure Messenger, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Configurer l’authentification unique TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Créer un utilisateur de test TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** pour avoir un équivalent de Britta Simon dans TigerText Secure Messenger, lié à l’utilisateur Azure AD nommé Britta Simon.
1. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de TigerText Secure Messenger, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **TigerText Secure Messenger**, sélectionnez **Authentification unique**.

    ![Configurer l’option d’authentification unique](common/select-sso.png)

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier** (icône de crayon) pour ouvrir le volet **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans le volet **Configuration SAML de base**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL TigerText Secure Messenger](common/sp-identifier.png)

    1. Dans la zone **URL de connexion**, entrez une URL :

       `https://home.tigertext.com`

    1. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant :

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > La valeur de **Identificateur (ID d’entité)** n’est pas réelle. Mettez-la à jour avec l’identificateur réel. Contactez l’[équipe du support technique de TigerText Secure Messenger](mailto:prosupport@tigertext.com) pour obtenir cette valeur. Vous pouvez aussi vous référer aux modèles figurant dans le volet **Configuration SAML de base** sur le portail Azure.

1. Dans le volet **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** à partir des options données et enregistrez-le sur votre ordinateur.

    ![Option de téléchargement du fichier XML de métadonnées de fédération](common/metadataxml.png)

1. Dans la section **Configurer TigerText Secure Messenger**, copiez les URL dont vous avez besoin :

   * **URL de connexion**
   * **Identificateur Azure AD**
   * **URL de déconnexion**

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configurer l’authentification unique TigerText Secure Messenger

Pour configurer l’authentification unique côté TigerText Secure Messenger, vous devez envoyer le XML des métadonnées de fédération téléchargé et les URL copiées appropriées du Portail Azure à l’[équipe du support technique TigerText Secure Messenger](mailto:prosupport@tigertext.com). L’équipe TigerText Secure Messenger vérifiera que la connexion avec l’authentification unique SAML est correctement configurée des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, créez un utilisateur de test nommé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**   > **Utilisateurs** > **Tous les utilisateurs**.

    ![Options Utilisateurs et « Tous les utilisateurs »](common/users.png)

1. En haut de l’écran, sélectionnez **+ Nouvel utilisateur**.

    ![Option Nouvel utilisateur](common/new-user.png)

1. Dans le volet **Utilisateur**, effectuez les étapes suivantes :

    ![Volet Utilisateur](common/user-properties.png)

    1. Dans la zone **Nom**, entrez **BrittaSimon**.
  
    1. Dans la zone **Nom d’utilisateur**, entrez **BrittaSimon\@\<yourcompanydomain>.\<extension>** . Par exemple, **BrittaSimon\@contoso.com**.

    1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TigerText Secure Messenger.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications** > **TigerText Secure Messenger**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **TigerText Secure Messenger**.

    ![TigerText Secure Messenger dans la liste des applications](common/all-applications.png)

1. Dans le volet gauche, sous **GÉRER**, sélectionnez **Utilisateurs et groupes**.

    ![Option « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **+ Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

1. Dans le volet **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**, puis choisissez **Sélectionner** au bas du volet.

1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans le volet **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Au bas du volet, choisissez **Sélectionner**.

1. Dans le volet **Ajouter une attribution**, sélectionnez **Attribuer**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Créer un utilisateur de test TigerText Secure Messenger

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans TigerText Secure Messenger. Contactez l’[équipe du support technique TigerText Secure Messenger](mailto:prosupport@tigertext.com) pour ajouter Britta Simon à la plateforme TigerText Secure Messenger. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à partir du portail Mes applications.

Quand vous sélectionnez **TigerText Secure Messenger** sur le portail Mes applications, vous devez être connecté automatiquement à l’abonnement TigerText Secure Messenger pour lequel vous avez configuré l’authentification unique. Pour plus d’informations sur le portail Mes applications, consultez [Accéder aux applications du portail Mes applications et les utiliser](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Listes des tutoriels pour intégrer des applications SaaS à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
