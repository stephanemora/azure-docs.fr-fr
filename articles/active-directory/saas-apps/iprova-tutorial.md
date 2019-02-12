---
title: 'Didacticiel : Intégration d’Azure Active Directory à iProva | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: e07f893296ec20e8c722fc977f93a38a797fe5c9
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490054"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Tutoriel : Intégration d’Azure Active Directory à iProva

Dans ce didacticiel, vous allez apprendre à intégrer iProva à Azure Active Directory (Azure AD).
L’intégration d’iProva à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à iProva.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à iProva (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications SaaS (Software as a Service) à Azure AD, consultez la page [Qu’est-ce que l’accès aux applications et l’authentification unique à Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à iProva, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois sur le site web [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement iProva pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test :

* iProva prend en charge l’authentification unique lancée par le fournisseur de services.

## <a name="add-iprova-from-the-gallery"></a>Ajouter iProva à partir de la galerie

Pour configurer l’intégration d’iProva à Azure AD, ajoutez iProva, disponible dans la galerie, à votre liste d’applications SaaS managées.

Pour ajouter iProva à partir de la galerie, effectuez les étapes suivantes :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **iProva**. Sélectionnez **iProva** dans le volet de résultats, puis sélectionnez **Ajouter** pour ajouter l’application.

     ![iProva dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iProva avec un utilisateur de test appelé Britta Simon.
Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur iProva associé.

Pour configurer et tester l’authentification unique Azure AD avec iProva, suivez les indications des sections suivantes :

- [Récupérer des informations de configuration à partir d’iProva](#retrieve-configuration-information-from-iprova) pour préparer les étapes suivantes.
- [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
- [Configurer l’authentification unique iProva](#configure-iprova-single-sign-on) pour configurer les paramètres de l’authentification unique côté application.
- [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
- [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
- [Créer un utilisateur de test iProva](#create-an-iprova-test-user) pour avoir un équivalent de Britta Simon dans iProva lié à la représentation Azure AD associée.
- [Tester l’authentification unique](#test-single-sign-on) pour vérifier si la configuration fonctionne.

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

Pour configurer l’authentification unique Azure AD avec iProva, effectuez les étapes suivantes.

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **iProva**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Icône Modifier dans Configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes.

    a. Renseignez le champ **Identificateur** avec la valeur affichée derrière l’étiquette **EntityID** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    b. Renseignez le champ **URL de réponse** avec la valeur affichée derrière l’étiquette **Reply URL** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    c. Renseignez le champ **URL de connexion** avec la valeur affichée derrière l’étiquette **Sign-on URL** dans la page **iProva SAML2 info**. Cette page est toujours ouverte dans votre autre onglet de navigateur.

    ![Informations d’authentification unique dans Domaine et URL iProva](common/sp-identifier-reply.png)

5. L’application iProva attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Boîte de dialogue Attributs utilisateur](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez l’attribut de jeton SAML comme illustré dans l’image précédente. Effectuez les opérations suivantes.

    | Nom | Attribut source| Espace de noms |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Sélectionnez **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Revendications d’utilisateur](common/new-save-attribute.png)

    ![Boîte de dialogue Gérer les revendications des utilisateurs](common/new-attribute-details.png)

    b. Dans la zone de texte **Nom**, entrez le nom d’attribut affiché pour cette ligne.

    c. Dans la zone de texte **Espace de noms**, entrez la valeur d’espace de noms affichée pour cette ligne.

    d. Sélectionnez **Attribut** comme **Source**.

    e. Dans la liste **Attribut de la source**, entrez la valeur d’attribut affichée pour cette ligne.

    f. Sélectionnez **OK**.

    g. Sélectionnez **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML** , sélectionnez l’icône **Copier** pour copier l’**URL des métadonnées de fédération d’application** et enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurer l’authentification unique iProva

1. Connectez-vous à iProva avec le compte **Administrateur**.

2. Ouvrez le menu **Go to** (Atteindre).

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **General** (Général) dans le panneau **System settings** (Paramètres système).

5. Sélectionnez **Edit** (Modifier).

6. Faites défiler jusqu’à **Access control** (Contrôle d’accès).

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

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

    ![Liens Utilisateurs et groupes et Tous les utilisateurs](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans la zone **Nom**, entrez un nom tel que **BrittaSimon**.
  
    b. Dans la zone **Nom d’utilisateur**, entrez *yourname@yourcompanydomain.extension*. 
    Par exemple BrittaSimon@contoso.com.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iProva.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications** > **iProva**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **iProva** (iProva).

    ![Lien iProva dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien Utilisateurs et groupes](common/users-groups-blade.png)

4. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Boîte de dialogue Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**, puis choisissez **Sélectionner** au bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Sélectionner** au bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

### <a name="create-an-iprova-test-user"></a>Créer un utilisateur de test iProva

1. Connectez-vous à iProva avec le compte **Administrateur**.

2. Ouvrez le menu **Go to** (Atteindre).

3. Sélectionnez **Application management** (Gestion des applications).

4. Sélectionnez **Users** (Utilisateurs) dans le panneau **Users and user groups** (Utilisateurs et groupes d’utilisateurs).

5. Sélectionnez **Add** (Ajouter).

6. Dans la zone **Username** (Nom d’utilisateur), entrez *brittasimon@yourcompanydomain.extension*. 
    Par exemple BrittaSimon@contoso.com.

7. Dans la zone **Full name** (Nom complet), entrez un nom complet comme **BrittaSimon**.

8. Sélectionnez l’option **No password (use single sign-on)** (Aucun mot de passe (utiliser l’authentification unique)).

9. Dans la zone **E-mail address** (Adresse e-mail), entrez *yourname@yourcompanydomain.extension*. 
   Par exemple BrittaSimon@contoso.com.

10. Faites défiler jusqu’à la fin de la page, puis sélectionnez **Finish** (Terminer).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette iProva dans le panneau d’accès, vous devez être connecté automatiquement à l’application iProva pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva - How to configure SAML2 single sign-on](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx) (iProva - Comment configurer l’authentification unique SAML2)
