---
title: 'Didacticiel : Intégration d’Azure Active Directory à GitHub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 79a2bc9d517e3c292268a4a70f08936cb0325fbd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053085"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Didacticiel : Intégration d’Azure Active Directory à GitHub

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
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de GitHub à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-github-from-the-gallery"></a>Ajout de GitHub à partir de la galerie
Pour configurer l’intégration de GitHub à Azure AD, vous devez ajouter GitHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter GitHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **GitHub**, sélectionnez **GitHub** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![GitHub dans la liste des résultats](./media/github-tutorial/tutorial_github_addfromgallery.png)

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

1. Dans le portail Azure, dans la page d’intégration de l’application **GitHub**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/github-tutorial/tutorial_github_samlbase.png)

3. Dans la section **Domaine et URL GitHub**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL GitHub](./media/github-tutorial/tutorial_github_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://github.com/orgs/<entity-id>/sso`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Accédez à la section d’administration de GitHub pour extraire ces valeurs.

4. Dans la section **Attributs utilisateur**, sélectionnez user.mail en tant qu’**Identificateur utilisateur**.

    ![Configurer l'authentification unique](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/github-tutorial/tutorial_github_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/github-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de GitHub**, cliquez sur **Configurer GitHub** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de GitHub](./media/github-tutorial/tutorial_github_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

9. Accédez aux **Paramètres** et cliquez sur **Sécurité**

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Cochez la case **Activer l’authentification SAML**, révélant ainsi les champs de configuration de l’authentification unique. Utilisez en suite la valeur d’URL d’authentification unique pour mettre à jour l’URL d’authentification unique dans la configuration d’Azure AD.

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Configurez les champs suivants :

    a. Dans la zone de texte **URL de connexion**, collez l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    b. Dans la zone de texte **Émetteur**, collez la valeur **ID d’entité SAML** que vous avez copiée sur le Portail Azure.

    c. Ouvrez le certificat téléchargé à partir du portail Azure dans le bloc-notes et collez le contenu dans la zone de texte **Certificat public**.

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Cliquez sur **Tester la configuration SAML** pour vérifier l’absence d’échecs ou d’erreurs de validation pendant l’authentification unique (SSO).

    ![Paramètres](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/github-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/github-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/github-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
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

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à GitHub, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **GitHub**.

    ![Lien GitHub dans la liste des applications](./media/github-tutorial/tutorial_github_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque GitHub dans le panneau d’accès, vous devez être connecté automatiquement à votre application GitHub.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

