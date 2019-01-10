---
title: 'Didacticiel : Intégration d’Azure Active Directory à GitHub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: 41e8fb40f07a88cb6fa2108a38db7f973fccd2f9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607796"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Didacticiel : Intégration d’Azure Active Directory à GitHub

Dans ce didacticiel, vous allez apprendre à intégrer GitHub à Azure Active Directory (Azure AD).

L’intégration de GitHub à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à GitHub.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à GitHub (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à GitHub, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement GitHub pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de GitHub à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-github-from-the-gallery"></a>Ajout de GitHub à partir de la galerie
Pour configurer l’intégration de GitHub à Azure AD, vous devez ajouter GitHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter GitHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![image](./media/github-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/github-tutorial/a_select_app.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/github-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **GitHub**, sélectionnez **GitHub** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec GitHub, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur GitHub équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur GitHub associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec GitHub, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test GitHub](#create-a-github-test-user)** pour avoir un équivalent de Britta Simon dans GitHub qui soit associé à la représentation de l’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application GitHub.

**Pour configurer l’authentification unique Azure AD avec GitHub, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **GitHub**, sélectionnez **Authentification unique**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![image](./media/github-tutorial/tutorial_github_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://github.com/orgs/<entity-id>/sso`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Accédez à la section d’administration de GitHub pour extraire ces valeurs.

5. L’application GitHub attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![image](./media/github-tutorial/i3-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    a. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Dans la liste **Attribut de la source**, sélectionnez la valeur de l’attribut.

    c. Cliquez sur **Enregistrer**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Dans la section **Configurer GitHub**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![image](./media/github-tutorial/d1_samlsonfigure.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

10. Accédez aux **Paramètres** et cliquez sur **Sécurité**

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Cochez la case **Activer l’authentification SAML**, révélant ainsi les champs de configuration de l’authentification unique. Utilisez en suite la valeur d’URL d’authentification unique pour mettre à jour l’URL d’authentification unique dans la configuration d’Azure AD.

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Configurez les champs suivants :

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Dans la zone de texte **Sign on URL** (URL de connexion), collez l’**URL de connexion** que vous avez copiée sur le portail Azure.

    b. Dans la zone de texte **Issuer** (Émetteur), collez l’**Identificateur Azure AD** que vous avez copié sur le Portail Azure.

    c. Ouvrez le certificat téléchargé à partir du portail Azure dans le bloc-notes et collez le contenu dans la zone de texte **Certificat public**.

    d. Cliquez sur l’icône **Modifier** pour changer la **méthode de signature** et la **méthode Digest** de **RSA-SHA1** et **SHA1** en **RSA-SHA256** et **SHA256** comme indiqué ci-dessous.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

13. Cliquez sur **Tester la configuration SAML** pour vérifier l’absence d’échecs ou d’erreurs de validation pendant l’authentification unique (SSO).

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Cliquez sur **Enregistrer**.

> [!NOTE]
> L’authentification unique dans GitHub authentifie auprès d’une organisation dans GitHub, et ne remplace pas l’authentification GitHub. Par conséquent, si la session github.com de l’utilisateur a expiré, vous pouvez être invité à vous authentifier avec un ID ou un mot de passe GitHub pendant le processus d’authentification unique.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/github-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="create-a-github-test-user"></a>Créer un utilisateur de test GitHub

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

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GitHub.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/github-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque GitHub dans le panneau d’accès, vous devez être connecté automatiquement à votre application GitHub.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


