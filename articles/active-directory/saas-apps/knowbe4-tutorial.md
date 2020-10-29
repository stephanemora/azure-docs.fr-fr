---
title: 'Didacticiel : intégration d’Azure Active Directory à la formation de sensibilisation à la sécurité KnowBe4 | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et la formation de sensibilisation à la sécurité KnowBe4.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 083c454390327972da2c2e63175021f2d9c2bd8f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458997"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Didacticiel : intégration d’Azure Active Directory à la formation de sensibilisation à la sécurité KnowBe4

Dans ce didacticiel, vous allez apprendre à intégrer la formation de sensibilisation à la sécurité KnowBe4 à Azure Active Directory (Azure AD).
L’intégration de la formation de sensibilisation à la sécurité KnowBe4 à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à la formation de sensibilisation à la sécurité KnowBe4.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à la formation de sensibilisation à la sécurité KnowBe4 (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à la formation de sensibilisation à la sécurité KnowBe4, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement à la formation de sensibilisation à la sécurité KnowBe4 pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* La formation de sensibilisation à la sécurité KnowBe4 prend en charge l’authentification unique initiée par le **fournisseur de services**

* La formation de sensibilisation à la sécurité KnowBe4 prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Ajout de la formation de sensibilisation à la sécurité KnowBe4 à partir de la galerie

Pour configurer l’intégration de la formation de sensibilisation à la sécurité KnowBe4 à Azure AD, vous devez ajouter la formation de sensibilisation à la sécurité KnowBe4 à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter la formation de sensibilisation à la sécurité KnowBe4 à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory** .

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise** , puis sélectionnez l’option **Toutes les applications** .

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **formation de sensibilisation à la sécurité KnowBe4** , sélectionnez **Formation de sensibilisation à la sécurité KnowBe4** dans le volet de résultats, puis cliquez sur **Ajouter** pour ajouter l’application.

     ![Formation de sensibilisation à la sécurité KnowBe4 dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec la formation de sensibilisation à la sécurité KnowBe4, sur un utilisateur de test appelé **Britta Simon** .
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur de la formation de sensibilisation à la sécurité KnowBe4 associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec la formation de sensibilisation à la sécurité KnowBe4, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique avec la formation de sensibilisation à la sécurité KnowBe4](#configure-knowbe4-security-awareness-training-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test de la formation de sensibilisation à la sécurité KnowBe4](#create-knowbe4-security-awareness-training-test-user)** pour avoir un équivalent de Britta Simon dans la formation de sensibilisation à la sécurité KnowBe4, lié à la représentation de l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec la formation de sensibilisation à la sécurité KnowBe4, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Formation de sensibilisation à la sécurité KnowBe4** , sélectionnez **Authentification unique** .

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique** , sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML** , cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base** .

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base** , effectuez les étapes suivantes :

    ![Informations d’authentification unique de Domaine et URL de la formation de sensibilisation à la sécurité KnowBe4](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion** , saisissez une URL au format suivant : `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez l’[équipe de support client de formation de sensibilisation à la sécurité KnowBe4](mailto:support@KnowBe4.com) pour obtenir cette valeur. Vous pouvez également consulter le modèle figurant à la section **Configuration SAML de base** dans le portail Azure.

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez la valeur de chaîne suivante : `KnowBe4`

    > [!NOTE]
    > Cette valeur respecte la casse.

5. Dans la page **Configurer l’authentification unique avec SAML** , à la section **Certificat de signature SAML** , cliquez sur **Télécharger** pour télécharger le **Certificat (Raw)** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificateraw.png)

6. Dans la section **Configurer la formation de sensibilisation à la sécurité KnowBe4** , copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Configurer l’authentification unique pour la formation de sensibilisation à la sécurité KnowBe4

Pour configurer l’authentification unique du côté de la **formation de sensibilisation à la sécurité KnowBe4** , vous devez envoyer le **Certificat (brut)** téléchargé et les URL appropriées copiées sur le portail Azure à l’ [équipe du support technique de la formation de sensibilisation à la sécurité KnowBe4](mailto:support@KnowBe4.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory** , sélectionnez **Utilisateurs** , puis sélectionnez **Tous les utilisateurs** .

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom** , entrez **BrittaSimon** .
  
    b. Dans le champ **Nom d’utilisateur** , tapez **brittasimon\@domainedevotreentreprise.extension** .  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe** , puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer** .

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à la formation de sensibilisation à la sécurité KnowBe4.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** , **Toutes les applications** , puis **formation de sensibilisation à la sécurité KnowBe4** .

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Formation de sensibilisation à la sécurité KnowBe4** .

    ![Lien Formation de sensibilisation à la sécurité KnowBe4 dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes** .

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur** , puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution** .

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes** , sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle** , sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution** , cliquez sur le bouton **Attribuer** .

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Créer un utilisateur de test de la formation de sensibilisation à la sécurité KnowBe4

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans la formation de sensibilisation à la sécurité KnowBe4. La formation de sensibilisation à la sécurité KnowBe4 prend en charge l’approvisionnement juste à temps, activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un nouvel utilisateur est créé lors d’une tentative d’accès à la formation de sensibilisation à la sécurité KnowBe4 s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique de formation de sensibilisation à la sécurité KnowBe4](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Formation de sensibilisation à la sécurité KnowBe4 dans le volet d’accès, vous devez être connecté automatiquement à l’application Formation de sensibilisation à la sécurité KnowBe4 pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)