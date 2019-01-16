---
title: 'Tutoriel : Intégration d’Azure Active Directory à GitHub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 41a7a11debfccfcde3258e17a337b5c72732dbf4
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065849"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutoriel : Intégration d’Azure Active Directory à GitHub

Dans ce didacticiel, vous allez apprendre à intégrer GitHub à Azure Active Directory (Azure AD).
L’intégration de GitHub à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à GitHub.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à GitHub (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à GitHub, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement GitHub pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* GitHub prend en charge l’authentification unique initiée par le **fournisseur de services**

* GitHub prend en charge l’[attribution d’utilisateurs **automatique**](github-provisioning-tutorial.md).

## <a name="adding-github-from-the-gallery"></a>Ajout de GitHub à partir de la galerie

Pour configurer l’intégration de GitHub à Azure AD, vous devez ajouter GitHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter GitHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **GitHub**, sélectionnez **GitHub** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![GitHub dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec GitHub, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur GitHub associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec GitHub, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique GitHub](#configure-github-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test GitHub](#create-github-test-user)** pour avoir un équivalent de Britta Simon dans GitHub qui soit associé à la représentation de l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec GitHub, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **GitHub**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL GitHub](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://github.com/orgs/<entity-id>/sso`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Accédez à la section d’administration de GitHub pour extraire ces valeurs.

5. L’application GitHub s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur unique** est **user.userprincipalname**, mais GitHub s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | NOM | Attribut source|
    | ---------------| --------------- |
    | Identificateur d’utilisateur unique | User.mail |
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer GitHub**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-github-single-sign-on"></a>Configurer l’authentification unique GitHub

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

2. Accédez aux **Paramètres** et cliquez sur **Sécurité**

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Cochez la case **Activer l’authentification SAML**, révélant ainsi les champs de configuration de l’authentification unique. Utilisez en suite la valeur d’URL d’authentification unique pour mettre à jour l’URL d’authentification unique dans la configuration d’Azure AD.

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configurez les champs suivants :

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Dans la zone de texte **Sign on URL** (URL de connexion), collez l’**URL de connexion** que vous avez copiée sur le portail Azure.

    b. Dans la zone de texte **Issuer** (Émetteur), collez l’**Identificateur Azure AD** que vous avez copié sur le Portail Azure.

    c. Ouvrez le certificat téléchargé à partir du portail Azure dans le bloc-notes et collez le contenu dans la zone de texte **Certificat public**.

    d. Cliquez sur l’icône **Modifier** pour changer la **méthode de signature** et la **méthode Digest** de **RSA-SHA1** et **SHA1** en **RSA-SHA256** et **SHA256** comme indiqué ci-dessous.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Cliquez sur **Tester la configuration SAML** pour vérifier l’absence d’échecs ou d’erreurs de validation pendant l’authentification unique (SSO).

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Cliquez sur **Enregistrer**.

> [!NOTE]
> L’authentification unique dans GitHub authentifie auprès d’une organisation dans GitHub, et ne remplace pas l’authentification GitHub. Par conséquent, si la session github.com de l’utilisateur a expiré, vous pouvez être invité à vous authentifier avec un ID ou un mot de passe GitHub pendant le processus d’authentification unique.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GitHub.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **GitHub**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **GitHub**.

    ![Lien GitHub dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-github-test-user"></a>Créer un utilisateur de test GitHub

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans GitHub. GitHub prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez plus d’informations [ici](github-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

2. Cliquez sur **People**.

    ![Personnes](./media/github-tutorial/tutorial_github_config_github_08.png "Personnes")

3. Cliquez sur **Invite member**.

    ![Inviter des utilisateurs](./media/github-tutorial/tutorial_github_config_github_09.png "inviter des utilisateurs")

4. Dans la boîte de dialogue **Invite member**, procédez comme suit :

    a. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    ![Inviter des personnes](./media/github-tutorial/tutorial_github_config_github_10.png "inviter des personnes")

    b. Cliquez sur **Send Invitation**.

    ![Inviter des personnes](./media/github-tutorial/tutorial_github_config_github_11.png "inviter des personnes")

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette GitHub dans le volet d’accès, vous devez être connecté automatiquement à l’application GitHub pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)