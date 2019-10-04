---
title: 'Didacticiel : Intégration d’Azure Active Directory à AwardSpring | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AwardSpring.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db00e0b6ea14e956fc75c2c7cff5d018d9c6bb84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106479"
---
# <a name="tutorial-azure-active-directory-integration-with-awardspring"></a>Didacticiel : Intégration d’Azure Active Directory à AwardSpring

Dans ce tutoriel, vous allez découvrir comment intégrer AwardSpring à Azure Active Directory (Azure AD).
L’intégration d’AwardSpring dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à AwardSpring
* Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à AwardSpring (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec AwardSpring, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement AwardSpring pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AwardSpring prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* AwardSpring prend en charge le provisionnement d’utilisateurs **Juste-à-temps**

## <a name="adding-awardspring-from-the-gallery"></a>Ajout d’AwardSpring à partir de la galerie

Pour configurer l’intégration d’AwardSpring à Azure AD, vous devez ajouter AwardSpring à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter AwardSpring à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **AwardSpring**, sélectionnez **AwardSpring** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![AwardSpring dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AwardSpring avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur AwardSpring associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AwardSpring, vous devez suivre les opérations suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique AwardSpring](#configure-awardspring-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test AwardSpring](#create-awardspring-test-user)** pour avoir un équivalent de Britta Simon dans AwardSpring, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec AwardSpring, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **AwardSpring**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique Domaine et URL AwardSpring](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.awardspring.com/SignIn/SamlAcs`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique Domaine et URL AwardSpring](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.awardspring.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique AwardSpring](mailto:support@awardspring.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application AwardSpring attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    |  Nom | Attribut source |
    | ---------------| --------------- |    
    | Prénom | user.givenname |
    | Nom | user.surname |
    | Email | user.mail |
    | Nom d’utilisateur | user.userprinicipalname |
    | StudentID | <ID_étudiant> |

    > [!NOTE]
    > L’attribut StudentID est mappé à l’ID d’étudiant réel qui doit être passé dans les revendications. Pour obtenir cette valeur, contactez l’[équipe du support client AwardSpring](mailto:support@awardspring.com).

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

9. Dans la section **Configurer AwardSpring**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-awardspring-single-sign-on"></a>Configurer l’authentification unique AwardSpring

Pour configurer l’authentification unique côté **AwardSpring**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL appropriées copiées à partir du portail Azure à l’[équipe du support technique AwardSpring](mailto:support@awardspring.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AwardSpring.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **AwardSpring**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **AwardSpring**.

    ![Le lien AwardSpring dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-awardspring-test-user"></a>Créer un utilisateur de test AwardSpring

Dans cette section, un utilisateur appelé Britta Simon est créé dans AwardSpring. AwardSpring prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans AwardSpring, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez  [l’équipe du support technique d’AwardSpring](maito:support@awardspring.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette AwardSpring dans le volet d’accès, vous devez être connecté automatiquement à l’application AwardSpring pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
