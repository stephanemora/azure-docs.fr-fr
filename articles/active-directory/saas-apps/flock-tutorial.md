---
title: 'Tutoriel : Intégration d’Azure Active Directory à Flock | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Flock.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 633828cc7e244dca1e43c45852910211aeda68d9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229493"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Tutoriel : Intégration d’Azure Active Directory à Flock

Dans ce tutoriel, vous allez apprendre à intégrer Flock Suite à Azure Active Directory (Azure AD).

L’intégration de Flock à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Flock.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Flock (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Flock, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Flock pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Flock à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-flock-from-the-gallery"></a>Ajouter Flock à partir de la galerie
Pour configurer l’intégration de Flock à Azure AD, vous devez ajouter Flock à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Flock à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Flock**, sélectionnez **Flock** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Flock dans la liste des résultats](./media/flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Flock pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quel utilisateur Flock correspond à l’utilisateur Azure AD. En d’autres termes, il est nécessaire d’établir une relation entre un utilisateur Azure AD et l’utilisateur Flock associé.

Pour configurer et tester l’authentification unique Azure AD avec Flock, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Flock](#create-a-flock-test-user)** pour avoir un équivalent de Britta Simon dans Flock, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application Flock.

**Pour configurer l’authentification unique Azure AD avec Flock, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **Flock** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/flock-tutorial/tutorial_flock_samlbase.png)

3. Dans la section **Domaine et URL Flock**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Flock](./media/flock-tutorial/tutorial_flock_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.flock.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support client de Flock](mailto:support@flock.com).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/flock-tutorial/tutorial_flock_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/flock-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Flock**, cliquez sur **Configurer Flock** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Flock](./media/flock-tutorial/tutorial_flock_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Flock en tant qu’administrateur.

8. Sélectionnez l’onglet **Authentification** dans le volet de navigation de gauche, puis **Authentification SAML**.

    ![Configuration de Flock](./media/flock-tutorial/configure1.png)

9. Dans la section **SAML Authentication** , procédez comme suit :

    ![Configuration de Flock](./media/flock-tutorial/configure2.png)

    a. Dans la zone de texte **Point de terminaison SAML 2.0 (HTTP)**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée sur le Portail Azure.

    b. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur **ID d’entité SAML** que vous avez copiée sur le Portail Azure.

    c. Ouvrez le **Certificat (Base64)** téléchargé sur le Portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificat public**.

    d. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/flock-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/flock-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/flock-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/flock-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-flock-test-user"></a>Créer un utilisateur de test Flock

Les utilisateurs Azure AD doivent avoir fait l’objet d’une attribution dans Flock pour pouvoir s’y connecter. Il s’agit en l’occurrence d’une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Flock en tant qu’administrateur.

2. Cliquez sur **Gérer l’équipe** dans le volet de navigation de gauche.

    ![Ajouter un employé](./media/flock-tutorial/user1.png)

3. Cliquez sur l’onglet **Ajouter un membre**, puis sélectionnez **Membres de l’équipe**.

    ![Ajouter un employé](./media/flock-tutorial/user2.png)

4. Entrez l’adresse e-mail de l’utilisateur, par exemple, **Brittasimon@contoso.com**, puis sélectionnez **Ajouter des utilisateurs**.

    ![Ajouter un employé](./media/flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Flock.

![Attribuer le rôle utilisateur][200]

**Pour attribuer Britta Simon à Flock, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Flock**.

    ![Lien Flock dans la liste Applications](./media/flock-tutorial/tutorial_flock_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Flock dans le volet d’accès, la connexion à votre application Flock doit se faire automatiquement.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/flock-tutorial/tutorial_general_01.png
[2]: ./media/flock-tutorial/tutorial_general_02.png
[3]: ./media/flock-tutorial/tutorial_general_03.png
[4]: ./media/flock-tutorial/tutorial_general_04.png

[100]: ./media/flock-tutorial/tutorial_general_100.png

[200]: ./media/flock-tutorial/tutorial_general_200.png
[201]: ./media/flock-tutorial/tutorial_general_201.png
[202]: ./media/flock-tutorial/tutorial_general_202.png
[203]: ./media/flock-tutorial/tutorial_general_203.png
