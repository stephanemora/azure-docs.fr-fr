---
title: "Didacticiel : Intégration d'Azure Active Directory à LinkedIn Sales Navigator | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Sales Navigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a216e720523767f428036290aea7151c2dca34
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708171"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Didacticiel : Intégration d'Azure Active Directory à LinkedIn Sales Navigator

Dans ce didacticiel, vous allez apprendre à intégrer LinkedIn Sales Navigator dans Azure Active Directory (Azure AD).
L’intégration de LinkedIn Sales Navigator dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à LinkedIn Sales Navigator.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LinkedIn Sales Navigator (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans LinkedIn Sales Navigator, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement LinkedIn Sales Navigator pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* LinkedIn Sales Navigator prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**

* LinkedIn Sales Navigator prend en charge l’attribution d’utilisateurs **juste-à-temps**

* LinkedIn Sales Navigator prend en charge l’attribution d’utilisateurs [juste-à-temps **automatisée**](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Ajout de LinkedIn Sales Navigator à partir de la galerie

Pour configurer l’intégration de LinkedIn Sales Navigator avec Azure AD, vous devez ajouter LinkedIn Sales Navigator à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter LinkedIn Sales Navigator à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, saisissez **LinkedIn Sales Navigator**, sélectionnez **LinkedIn Sales Navigator** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![LinkedIn Sales Navigator dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LinkedIn Sales Navigator par le biais d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur LinkedIn Sales Navigator associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Sales Navigator, vous devez vous conformer aux instructions des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** pour avoir un équivalent de Britta Simon dans LinkedIn Sales Navigator lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec LinkedIn Sales Navigator, effectuez les étapes suivantes :

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **LinkedIn Sales Navigator**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL LinkedIn Sales Navigator](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur **Entity ID** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

    b. Dans la zone de texte **URL de réponse**, entrez la valeur **Assertion Consumer Access (ACS) URL** (la procédure à suivre pour copier cette valeur à partir du portail Linkedin est expliquée plus loin dans ce tutoriel).

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL LinkedIn Sales Navigator](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. L’application LinkedIn Sales Navigator attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application LinkedIn Sales Navigator s’attend à ce que nameidentifier soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône Modifier.

    ![image](common/edit-attribute.png)

7. Outre ceux mentionnés ci-dessus, l’application LinkedIn Sales Navigator s’attend également à ce que quelques attributs de plus soient transmis dans la réponse SAML. Dans la section Revendications des utilisateurs de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML, comme indiqué dans le tableau ci-dessous :

    | Nom | Attribut source|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer LinkedIn Sales Navigator**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>Configurer l’authentification unique pour LinkedIn Sales Navigator

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site web **LinkedIn Sales Navigator** en tant qu’administrateur.

1. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. En outre, sélectionnez **Sales Navigator** dans la liste déroulante.

    ![Configurer l'authentification unique](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Cliquez sur **OR Click Here to load and copy individual fields from the form** (OU cliquez ici pour charger et copier des champs individuels à partir du formulaire) et effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Copiez la valeur **Entity ID** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    b. Copiez la valeur **Assertion Consumer Access (ACS) URL** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

1. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option **Upload XML file** (Charger un fichier XML).

    ![Configurer l'authentification unique](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**

    ![Configurer l'authentification unique](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Sales Navigator.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **LinkedIn Sales Navigator**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **LinkedIn Sales Navigator**.

    ![Lien LinkedIn Sales Navigator dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-linkedin-sales-navigator-test-user"></a>Créer un utilisateur de test LinkedIn Sales Navigator

L’application LinkedIn Sales Navigator prend en charge la configuration d’utilisateur juste à temps (JIT), et après authentification, les utilisateurs sont créés automatiquement dans l’application. Activez **Affecter automatiquement les licences** pour affecter une licence à l’utilisateur.

   ![Création d’un utilisateur de test Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette LinkedIn Sales Navigator dans le volet d’accès, vous devez être connecté automatiquement à l’application LinkedIn Sales Navigator pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

