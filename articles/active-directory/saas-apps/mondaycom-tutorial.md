---
title: 'Didacticiel : Intégration d’Azure Active Directory avec monday.com | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896883"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Didacticiel : Intégration d’Azure Active Directory avec monday.com

Dans ce tutoriel, vous allez apprendre à intégrer monday.com avec Azure Active Directory (Azure AD).

L’intégration de monday.com à Azure AD vous offre les avantages suivants :

* Vous pouvez utiliser Azure AD pour contrôler qui a accès à monday.com.
* Les utilisateurs peuvent être automatiquement connectés à monday.com avec leur compte Azure AD (par le biais de l'authentification unique).
* Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications software as a service (SaaS) à Azure AD, consultez l’article [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec monday.com, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’abonnement Azure AD, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un abonnement monday.com pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test et intégrer monday.com à Azure AD.

monday.com prend en charge les fonctionnalités suivantes :

* **Authentification unique lancée par le fournisseur de services**
* **Authentification unique démarrée par le fournisseur d’identité**
* **Attribution d’utilisateurs juste-à-temps**

## <a name="add-mondaycom-in-the-azure-portal"></a>Ajouter monday.com dans le portail Azure

Pour intégrer monday.com à Azure AD, vous devez ajouter monday.com à votre liste d’applications SaaS managées.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

    ![Option Azure Active Directory](common/select-azuread.png)

1. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Pour ajouter une application, sélectionnez **Nouvelle application**.

    ![Option Nouvelle application](common/add-new-app.png)

1. Saisissez **monday.com** dans la zone de recherche. Dans les résultats de la recherche, sélectionnez **monday.com**, puis **Ajouter**.

    ![monday.com dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec monday.com sur un utilisateur de test nommé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur monday.com associé.

Pour configurer et tester l’authentification unique Azure AD avec monday.com, vous devez suivre les indications des sections suivantes :

| Tâche | Description |
| --- | --- |
| **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** | Autorise les utilisateurs à utiliser cette fonctionnalité. |
| **[Configurer l’authentification unique monday.com](#configure-mondaycom-single-sign-on)** | Configure les paramètres d’authentification unique dans l’application. |
| **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** | Teste l’authentification unique Azure AD pour un utilisateur appelé Britta Simon. |
| **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** | Active Britta Simon pour lui permettre d'utiliser l’authentification unique Azure AD. |
| **[Créer utilisateur de test monday.com](#create-a-mondaycom-test-user)** | Crée un équivalent de Britta Simon dans monday.com lié à la représentation Azure AD associée. |
| **[Tester l’authentification unique](#test-single-sign-on)** | Vérifie que la configuration fonctionne. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez configurer l’authentification unique Azure AD avec monday.com dans le portail Azure.

1. Sur le [portail Azure](https://portal.azure.com/), dans le volet d’intégration de l’application **monday.com**, cliquez sur **Authentification unique**.

    ![Configurer l’option d’authentification unique](common/select-sso.png)

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** ou **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier** (icône de crayon) pour ouvrir le volet **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans le volet **Configuration SAML de base**, si vous avez un fichier de métadonnées de fournisseur de services et voulez une configuration en *mode initié par le fournisseur d’identité* (IDP), effectuez les étapes suivantes :

    1. Sélectionnez **Charger le fichier de métadonnées**.

       ![Option Charger le fichier de métadonnées](common/upload-metadata.png)

    1. Pour sélectionner le fichier de métadonnées, sélectionnez l’icône de dossier, puis **Charger**.

       ![Sélectionnez le fichier de métadonnées, puis le bouton Charger](common/browse-upload-metadata.png)

    1. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont renseignées automatiquement dans le volet **Configuration SAML de base** :

       ![Valeurs du fournisseur d’identité dans le volet de configuration SAML de base](common/idp-intiated.png)

       > [!Note]
       > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas renseignées automatiquement, entrez-les manuellement.

1. Pour configurer l’application en mode *initié par le fournisseur de services* :

    1. Sélectionnez **Définir des URL supplémentaires**.
    
    1. Dans la zone **URL de connexion**, entrez une URL au format suivant : https:\//\<votre-domaine>.monday.com. Contactez l[’équipe de support technique monday.com](mailto:support@monday.com) pour obtenir l’URL de connexion.

        ![Option Définir des URL supplémentaires](common/metadata-upload-additional-signon.png)

1. L’application monday.com attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Pour gérer ces valeurs d’attributs, dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier** pour ouvrir le volet **Attributs utilisateur**.

    ![Volet Attributs utilisateur](common/edit-attribute.png)

1. Sous **Revendications utilisateur**, sélectionnez **Modifier** pour modifier les revendications. Pour ajouter une revendication, sélectionnez **Ajouter une nouvelle revendication**, puis configurez l’attribut de jeton SAML comme illustré dans l’image précédente. Effectuez ensuite les tâches suivantes : 

    1. Sélectionnez **Ajouter une nouvelle revendication**.

        ![Option Ajouter une nouvelle revendication dans le volet Revendications utilisateur](common/new-save-attribute.png)

    1. Dans le volet **Gérer les revendications des utilisateurs**, définissez les valeurs suivantes :
        
       1. Dans la zone de texte **Nom**, entrez le nom d’attribut affiché pour la ligne de revendication utilisateur.

       1. Laissez le champ **Espace de noms** vide.

       1. Dans **Source**, sélectionnez **Attribut**.

       1. Dans la liste **Attribut de la source**, sélectionnez la valeur d’attribut indiquée pour cette ligne de revendication utilisateur.

       1. Sélectionnez **OK**, puis cliquez sur **Enregistrer**.

       ![Gérer les revendications des utilisateurs](common/new-attribute-details.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sous **Certificat de signature SAML**, sélectionnez **Télécharger** en regard de **Certificat (Base64)** . Sélectionnez une option de téléchargement en fonction de vos exigences. Enregistrez le certificat sur votre ordinateur.

    ![Option de téléchargement du certificat (Base64)](common/certificatebase64.png)

1. Dans la section **Configurer monday.com**, copiez les URL suivantes en fonction de vos besoins :

    * URL de connexion
    * Identificateur Azure AD
    * URL de déconnexion

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Configurer l’authentification unique monday.com

Pour configurer l’authentification unique côté monday.com, envoyez le fichier de certificat (Base64) téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support technique monday.com](mailto:support@monday.com). L’équipe du support technique monday.com utilise les informations que vous lui envoyez pour vérifier que la connexion SAML à authentification unique est correctement configurée des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, créez un utilisateur de test nommé Britta Simon dans le portail Azure.

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

    ![Options Utilisateurs et Tous les utilisateurs](common/users.png)

1. Sélectionnez **Nouvel utilisateur**.

    ![Option Nouvel utilisateur](common/new-user.png)

1. Dans le volet **Utilisateur**, effectuez les étapes suivantes :

    1. Dans la zone **Nom**, entrez **BrittaSimon**.
  
    1. Dans la zone **Nom d’utilisateur**, entrez **brittasimon\@\<votre_domaine_d’entreprise>.\<extension>** . Par exemple, **brittasimon\@contoso.com**.

    1. Cochez la case **Afficher le mot de passe**. Notez la valeur affichée dans la zone **Mot de passe**.

    1. Sélectionnez **Créer**.

    ![Volet Utilisateur](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à accéder à monday.com pour lui permettre d'utiliser l’authentification unique Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications** > **monday.com**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **monday.com**.

    ![monday.com dans la liste des applications](common/all-applications.png)

1. Dans le menu, sélectionnez **Utilisateurs et groupes**.

    ![Option Utilisateurs et groupes](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

1. Dans le volet **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs. Choisissez **Select**.

1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans le volet **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Select**.

1. Dans le volet **Ajouter une attribution**, sélectionnez **Attribuer**.

### <a name="create-a-mondaycom-test-user"></a>Créer utilisateur de test monday.com

Dans cette section, un utilisateur appelé Britta Simon est créé dans l’application monday.com. monday.com prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans monday.com, il est créé après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à partir du portail Mes applications.

Après avoir configuré l’authentification unique, lorsque vous sélectionnez **monday.com** dans le portail Mes applications, vous êtes automatiquement connecté à monday.com. Pour plus d’informations sur le portail Mes applications, consultez [Accéder aux applications du portail Mes applications et les utiliser](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Listes des tutoriels pour intégrer des applications SaaS à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
