---
title: 'Tutoriel : Intégration d’Azure Active Directory à RStudio Connect | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RStudio Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a9b49f75ad377a9377a2311ed16c17ca3d749e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092574"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Tutoriel : Intégration d’Azure Active Directory à RStudio Connect

Dans ce tutoriel, vous allez apprendre à intégrer RStudio Connect à Azure Active Directory (Azure AD).
L’intégration de RStudio Connect à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à RStudio Connect.
* Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à RStudio Connect (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de RStudio Connect à Azure AD, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* RStudio Connect. Il existe une [évaluation gratuite de 45 jours.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* RStudio Connect prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**

* RStudio Connect prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-rstudio-connect-from-the-gallery"></a>Ajout de RStudio Connect à partir de la galerie

Pour configurer l’intégration de RStudio Connect à Azure AD, vous devez ajouter RStudio Connect, à partir de la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter RStudio Connect à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **RStudio Connect**, sélectionnez **RStudio Connect** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![RStudio Connect dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de RStudio Connect avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur RStudio Connect associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD auprès de RStudio Connect, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique RStudio Connect](#configure-rstudio-connect-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test RStudio Connect](#create-rstudio-connect-test-user)** pour avoir un équivalent de Britta Simon dans RStudio Connect lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de RStudio Connect, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **RStudio Connect**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes, en remplaçant `<example.com>` par l’adresse et le port de votre serveur RStudio Connect :

    ![Informations d’authentification unique dans Domaine et URL RStudio Connect](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<example.com>/__login__/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<example.com>/__login__/saml/acs`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL RStudio Connect](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<example.com>/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Elles sont déterminées à partir de l’adresse du serveur RStudio Connect (`https://example.com` dans les exemples ci-dessus). Contactez l’[équipe de support RStudio Connect](mailto:support@rstudio.com) si vous rencontrez des problèmes. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Votre application RStudio Connect s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application RStudio Connect s’attend à ce que **nameidentifier** soit mappé sur **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Configurer l’authentification unique RStudio Connect

Pour configurer l’authentification unique pour **RStudio Connect**, vous devez utiliser l’**URL des métadonnées de fédération d'application** et l’**adresse du serveur** utilisées ci-dessus. Cette opération est effectuée dans le fichier de configuration RStudio Connect dans `/etc/rstudio-connect.rstudio-connect.gcfg`.

Voici un exemple de fichier de configuration :

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Stockez l’**adresse du serveur** dans la valeur `Server.Address`, et l’**URL des métadonnées de fédération d'application** dans la valeur `SAML.IdPMetaData`.

Si vous rencontrez des problèmes de configuration, vous pouvez lire le [Guide de l’administrateur RStudio Connect](https://docs.rstudio.com/connect/admin/authentication.html#authentication-saml) ou envoyer un e-mail à l’[équipe du support technique de RStudio](mailto:support@rstudio.com) pour obtenir de l’aide.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RStudio Connect.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **RStudio Connect**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **RStudio Connect**.

    ![Lien RStudio Connect dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-rstudio-connect-test-user"></a>Créer un utilisateur de test RStudio Connect

Dans cette section, un utilisateur appelé Britta Simon est créé dans RStudio Connect. RStudio Connect prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’y a pas déjà un utilisateur dans RStudio Connect, un utilisateur est créé lorsque vous tentez d’y accéder.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette RStudio Connect dans le volet d’accès, vous devez être automatiquement connecté à l’application RStudio Connect pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

