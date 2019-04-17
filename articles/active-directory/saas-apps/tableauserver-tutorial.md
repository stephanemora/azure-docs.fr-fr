---
title: 'Didacticiel : Intégration d’Azure Active Directory dans Tableau Server | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ae0aefe194ca8bca6ea62420314b4fbdb1e0187
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357933"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Didacticiel : Intégration d’Azure Active Directory dans Tableau Server

Dans ce didacticiel, vous allez apprendre à intégrer Tableau Server avec Azure Active Directory (Azure AD).
L’intégration de Tableau Server à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Tableau Server.
* Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à Tableau Server (authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Tableau Server, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement Tableau Server avec l’authentification unique activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Tableau Server prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-tableau-server-from-the-gallery"></a>Ajout de Tableau Server à partir de la galerie

Pour configurer l’intégration de Tableau Server à Azure AD, vous devez ajouter Tableau Server, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Tableau Server à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Tableau Server**, sélectionnez **Tableau Server** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Tableau Server dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Tableau Server à l’aide d’un utilisateur de test, **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Tableau Server associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Server, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Tableau Server](#configure-tableau-server-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Tableau Server](#create-tableau-server-test-user)**  : pour avoir un équivalent de Britta Simon dans Tableau Server lié à la représentation d’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Tableau Server, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Tableau Server**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Tableau Server](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL d’authentification**, tapez une URL au format suivant : `https://azure.<domain name>.link`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://azure.<domain name>.link`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas des valeurs réelles. Mettez à jour les valeurs avec l’URL et l’identificateur réels à partir de la page de configuration de Tableau Server, conformément aux explications données plus loin dans le tutoriel.

5. L’application Tableau Server attend une revendication personnalisée **nom d’utilisateur** qui doit être définie comme suit. Cela est utilisé comme identifiant utilisateur au lieu de la revendication Identifiant unique de l’utilisateur. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs et revendications de l’utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur et revendications**, configurez le jeton SAML comme sur l’image ci-dessus, puis effectuez les étapes suivantes :

    | Nom | Attribut source | Espace de noms |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Entrez la valeur **Espace de noms**.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Tableau Server**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-tableau-server-single-sign-on"></a>Configurer l’authentification unique Tableau Server

1. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Tableau Server comme administrateur.

2. Sur l’onglet **CONFIGURATION**, sélectionnez **User Identity & Access** (Identité utilisateur et accès), puis l’onglet de la méthode d’**authentification**.

    ![Configurer l'authentification unique](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Dans la page **CONFIGURATION**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Pour **Méthode d’authentification**, sélectionnez SAML.

    b. Cochez la case **Enable SAML Authentication for the server** (Activer l’authentification SAML pour le serveur).

    c. Tableau Server return URL (URL de retour Tableau Server) : URL à laquelle accèdent les utilisateurs Tableau Server, telle que <http://tableau_server>. L’utilisation de `http://localhost` n’est pas recommandée. L’utilisation d’une URL avec une barre oblique finale (par exemple, `http://tableau_server/`) n’est pas prise en charge. Copiez l’**URL de retour de Tableau Server** et collez-la dans la zone de texte **URL de connexion** située dans la section **Configuration SAML de base** dans le portail Azure

    d. SAML entity ID (ID d’entité SAML) : l’ID d’entité identifie de façon unique votre installation Tableau Server auprès du fournisseur d’identité. Vous pouvez à nouveau entrer l’URL Tableau Server ici, si vous le souhaitez, mais ce n’est pas obligatoire. Copiez l’**ID d’entité SAML** et collez-la dans la zone de texte **Identificateur** située dans la section **Configuration SAML de base** dans le portail Azure

    e. Cliquez sur **Download XML Metadata File** (Télécharger le fichier de métadonnées XML) et ouvrez-le dans l’application de l’éditeur de texte. Recherchez l’URL Assertion Consumer Service avec HTTP POST et Index 0, puis copiez l’URL. Maintenant collez-la dans la zone de texte **URL de réponse** située dans la section **Configuration SAML de base** dans le portail Azure

    f. Localisez votre fichier de métadonnées de fédération téléchargé à partir du portail Azure, puis chargez-le dans le **fichier de métadonnées du fournisseur d’identité SAML**.

    g. Entrez les noms des attributs que le fournisseur d’identité utilise pour accueillir les noms d’utilisateur, noms d’affichage et adresses e-mail.

    h. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Le client doit charger l’ensemble des certificats dans la configuration SAML SSO de Tableau Server. Les certificats seront ignorés dans le flux SSO. Si vous avez besoin d’aide pour la configuration de SAML dans Tableau Server, consultez l’article [Configurer SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tableau Server.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Tableau Server**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Tableau Server**.

    ![Lien vers Tableau Server dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-tableau-server-test-user"></a>Créer un utilisateur de test Tableau Server

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Tableau Server. Vous devez approvisionner tous les utilisateurs dans Tableau Server.

Le nom de l’utilisateur doit correspondre à la valeur que vous avez configurée dans l’attribut personnalisé Azure AD **username**. Avec le mappage correct, l’intégration doit prendre en charge Configuration de l’authentification unique Azure AD.

> [!NOTE]
> Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter l’administrateur Tableau Server de votre organisation.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Tableau Server dans le panneau d’accès, vous devez être automatiquement connecté à l’application Tableau Server pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [ Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

