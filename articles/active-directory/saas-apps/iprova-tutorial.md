---
title: 'Didacticiel : Intégration d’Azure Active Directory à iProva | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5b66b3cf2ec038107293da7a821fcb75273fc9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718339"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Didacticiel : Intégration d’Azure Active Directory à iProva

Dans ce didacticiel, vous allez apprendre à intégrer iProva à Azure Active Directory (Azure AD).
L’intégration d’iProva à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à iProva.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à iProva (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à iProva, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement iProva pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* iProva prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

## <a name="adding-iprova-from-the-gallery"></a>Ajout d’iProva à partir de la galerie

Pour configurer l’intégration d’iProva à Azure AD, vous devez ajouter iProva, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter iProva à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **iProva**, sélectionnez **iProva** (iProva) dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![iProva dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iProva avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur iProva associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec iProva, vous devez suivre les indications des sections suivantes :

1. **[Récupérer des informations de configuration à partir d’iProva](#retrieve-configuration-information-from-iprova)** pour préparer les étapes suivantes.
2. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
3. **[Configurer l’authentification unique iProva](#configure-iprova-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
4. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
5. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Créer un utilisateur de test iProva](#create-iprova-test-user)** pour avoir un équivalent de Britta Simon dans iProva lié à la représentation Azure AD associée.
7. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="retrieve-configuration-information-from-iprova"></a>Récupérer des informations de configuration à partir d’iProva

Dans cette section, vous allez récupérer des informations à partir d’iProva pour configurer l’authentification unique Azure AD.

1. Ouvrez un navigateur web et accédez à la page **SAML2 info** dans iProva, en utilisant le modèle d’URL suivant :

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Afficher la page d’informations SAML2 iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Laissez l’onglet du navigateur ouvert pendant que vous effectuez les étapes qui suivent dans un autre onglet de navigateur.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec iProva, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **iProva**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL iProva](common/sp-identifier-reply.png)

    a. Renseignez le champ **Identificateur** avec la valeur affichée derrière l’étiquette **EntityID** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    b. Renseignez le champ **URL de réponse** avec la valeur affichée derrière l’étiquette **Reply URL** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    c. Renseignez le champ **URL de connexion** avec la valeur affichée derrière l’étiquette **Sign-on URL** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

5. L’application iProva attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom | Attribut source| Espace de noms  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurer l’authentification unique iProva

1. Connectez-vous à iProva avec le compte **Administrateur**.

2. Ouvrez le menu **Atteindre**.

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **General** (Général) dans le panneau **System settings** (Paramètres système).

5. Sélectionnez **Modifier**.

6. Faites défiler jusqu’à **Contrôle d’accès**.

    ![Paramètres de contrôle d’accès iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Recherchez le paramètre **Users are automatically logged on with their network accounts** (Les utilisateurs sont automatiquement connectés avec leur compte réseau) et remplacez-le par **Yes, authentication via SAML** (Oui, authentification via SAML). Des options supplémentaires s’affichent maintenant.

8. Sélectionnez **Set up** (Configurer).

9. Sélectionnez **Suivant**.

10. iProva vous demande si vous souhaitez télécharger des données de fédération à partir d’une URL ou les charger à partir d’un fichier. Sélectionnez l’option **From URL** (À partir d’une URL).

    ![Télécharger des métadonnées Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Collez l’URL de métadonnées que vous avez enregistrée lors de la dernière étape de la section « Configurer l’authentification unique Azure AD ».

12. Sélectionnez le bouton en forme de flèche pour télécharger les métadonnées à partir d’Azure AD.

13. Une fois le téléchargement terminé, le message de confirmation **Valid Federation Data file downloaded** (Fichier de données de fédération valide téléchargé) s’affiche.

14. Sélectionnez **Suivant**.

15. Ignorez l’option **Test login** (Tester la connexion) pour l’instant et sélectionnez **Suivant**.

16. Dans la zone de liste déroulante **Claim to use** (Revendication à utiliser), sélectionnez **windowsaccountname**.

17. Sélectionnez **Terminer**.

18. Vous revenez à présent à l’écran **Edit general settings** (Modifier les paramètres généraux). Faites défiler jusqu’en bas de la page et sélectionnez **OK** pour enregistrer votre configuration.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iProva.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **iProva** (iProva).

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **iProva** (iProva).

    ![Lien iProva dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-iprova-test-user"></a>Créer un utilisateur de test iProva

1. Connectez-vous à iProva avec le compte **Administrateur**.

2. Ouvrez le menu **Atteindre**.

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **Users** (Utilisateurs) dans le panneau **Users and user groups** (Utilisateurs et groupes d’utilisateurs).

5. Sélectionnez **Ajouter**.

6. Dans la zone de texte **Username** (Nom d’utilisateur), entrez le nom d’un utilisateur, par exemple `BrittaSimon@contoso.com`.

7. Dans la zone **Full name** (Nom complet), entrez un nom complet, comme **BrittaSimon**.

8. Sélectionnez l’option **No password (use single sign-on)** (Aucun mot de passe (utiliser l’authentification unique)).

9. Dans la zone de texte **Email address** (Adresse e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple `BrittaSimon@contoso.com`.

10. Faites défiler jusqu’à la fin de la page, puis sélectionnez **Finish** (Terminer).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette iProva dans le panneau d’accès doit vous connecter automatiquement à l’application iProva pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)