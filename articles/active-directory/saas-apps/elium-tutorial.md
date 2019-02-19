---
title: 'Didacticiel : Intégration d’Azure Active Directory à Elium | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85d9c56fa1f0da5bf4747693ebe2361205e83ba0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196737"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Didacticiel : Intégration d’Azure Active Directory à Elium

Dans ce tutoriel, vous allez découvrir comment intégrer Elium à Azure Active Directory (Azure AD).
L’intégration d’Elium dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Elium.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Elium (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Elium, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Elium pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Elium prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**.

* Elium prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-elium-from-the-gallery"></a>Ajout d’Elium depuis la galerie

Pour configurer l’intégration d’Elium à Azure AD, vous devez ajouter Elium, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Elium à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Elium**, sélectionnez **Elium** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Elium dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Elium sur un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez associer l’utilisateur Azure AD à l’utilisateur Elium.

Pour configurer et tester l’authentification unique Azure AD avec Elium, vous devez suivre les indications des modules suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Elium](#configure-elium-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Elium](#create-elium-test-user)** pour avoir dans Elium un équivalent de Britta Simon associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Elium, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Elium**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Elium](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/acs`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Elium](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous obtenez ces valeurs à partir du **fichier de métadonnées SP** téléchargeable à l’adresse `https://<platform-domain>.elium.com/login/saml2/metadata`, procédure qui est expliquée plus loin dans ce tutoriel.

6. L’application Elium s’attend à ce que les assertions SAML soient dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit : 

    | Nom | Attribut source|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | fonction| user.jobtitle|
    | société| user.companyname|

    > [!NOTE]
    > Ce sont les revendications par défaut. **Seule la revendication d’e-mail est obligatoire**. Pour le provisionnement JIT, cette revendication est également la seule obligatoire. D’autres revendications personnalisées peuvent varier d’une plateforme de client à l’autre.

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer Elium**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-elium-single-sign-on"></a>Configurer l’authentification unique Elium

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Elium en tant qu’administrateur.

2. Cliquez sur le **Profil utilisateur** dans le coin supérieur droit, puis sélectionnez **Administration**.

    ![Configurer l'authentification unique](./media/elium-tutorial/user1.png)

3. Sélectionnez l’onglet **Sécurité**.

    ![Configurer l'authentification unique](./media/elium-tutorial/user2.png)

4. Faites défiler jusqu’à la section **Single Sign-On SSO** (Authentification unique) et effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/elium-tutorial/user3.png)

    a. Copiez la valeur de **Verify that SAML2 authentication works for your account** (Vérifiez que l’authentification SAML2 fonctionne pour votre compte) et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    > [!NOTE]
    > Après avoir configuré l’authentification unique, vous conservez l’accès à la page de connexion à distance par défaut en utilisant l’URL suivante : `https://<platform_domain>/login/regular/login` 

    b. Sélectionnez la case à cocher **Enable SAML2 federation** (Activer la fédération SAML2).

    c. Sélectionnez la case à cocher **JIT Provisioning** (Provisionnement JIT).

    d. Ouvrez **SP Metadata** (Métadonnées SP) en cliquant sur le bouton **Download** (Télécharger).

    e. Recherchez l’**entityID** (identificateur d’utilisateur) dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **entityID**, puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure. 

    ![Configurer l'authentification unique](./media/elium-tutorial/user4.png)

    f. Recherchez **AssertionConsumerService** dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **Location**, puis collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    ![Configurer l'authentification unique](./media/elium-tutorial/user5.png)

    g. Dans le Bloc-notes, ouvrez le fichier de métadonnées téléchargé depuis le portail Azure, copiez son contenu et collez-le dans la zone de texte **IdP Metadata** (Métadonnées du fournisseur d’identité).

    h. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant un accès à Elium.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Elium**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Elium**.

    ![Lien Elium dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-elium-test-user"></a>Créer un utilisateur de test Elium

Dans cette section, un utilisateur appelé Britta Simon est créé dans Elium. Elium prend en charge l’**attribution d’utilisateurs juste-à-temps**, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas d’utilisateur dans Elium, un nouvel utilisateur est créé lorsque vous tentez d’y accéder.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Elium](mailto:support@elium.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Elium dans le volet d’accès, vous devez être connecté automatiquement à l’application Elium pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

