---
title: 'Tutoriel : Intégration d’Azure Active Directory à moconavi | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 3467b823e6c91d34ebd48c7f8bc29558a79c59e5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229544"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Tutoriel : Intégration d’Azure Active Directory à moconavi

Dans ce didacticiel, vous allez apprendre à intégrer moconavi à Azure Active Directory (Azure AD).

L’intégration de moconavi à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à moconavi.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à moconavi (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à moconavi, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement moconavi pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter moconavi à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-moconavi-from-the-gallery"></a>Ajouter moconavi à partir de la galerie
Pour configurer l’intégration de moconavi à Azure AD, vous devez ajouter moconavi à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter moconavi à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **moconavi**, sélectionnez **moconavi** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![moconavi dans la liste des résultats](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec moconavi pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quel utilisateur moconavi correspond à l’utilisateur Azure AD. En d’autres termes, il est nécessaire d’établir une relation entre un utilisateur Azure AD et l’utilisateur moconavi associé.

Pour configurer et tester l’authentification unique Azure AD avec moconavi, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Flock](#create-a-moconavi-test-user)** pour avoir un équivalent de Britta Simon dans moconavi, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application moconavi.

**Pour configurer l’authentification unique Azure AD avec moconavi, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **moconavi** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Dans la section **Domaine et URL moconavi**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL moconavi](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<yourserverurl>/moconavi-saml2`

    C. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support client de moconavi](mailto:support@recomot.co.jp).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/moconavi-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **moconavi**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support de moconavi](mailto:support@recomot.co.jp). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/moconavi-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/moconavi-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/moconavi-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-moconavi-test-user"></a>Créer un utilisateur de test moconavi

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans moconavi. Rapprochez-vous de [l’équipe de support de moconavi](mailto:support@recomot.co.jp) pour ajouter les utilisateurs sur la plateforme moconavi. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à moconavi.

![Attribuer le rôle utilisateur][200]

**Pour attribuer Britta Simon à moconavi, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **moconavi**.

    ![Lien moconavi dans la liste Applications](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

1. Installez moconavi à partir de Microsoft Store.

2. Lancez moconavi.

3. Cliquez sur le bouton **Paramètre de connexion**.

    ![Test de l’authentification unique](./media/moconavi-tutorial/testing1.png)

4. Entrez `https://mcs-admin.moconavi.biz/gateway` dans la zone de texte **Se connecter à l’URL**, puis cliquez sur le bouton **Terminé**.

    ![Test de l’authentification unique](./media/moconavi-tutorial/testing2.png)

5. Dans la capture d’écran suivante, suivez les étapes ci-dessous :

    ![Test de l’authentification unique](./media/moconavi-tutorial/testing3.png)

    a. Entrez la **clé d’authentification** `azureAD` dans la zone de texte **Entrer la clé d’authentification**.

    b. Entrez **l’ID d’utilisateur** `your ad account` dans la zone de texte **Entrer l’ID d’utilisateur**.

    c. Cliquez sur **CONNEXION**.

6. Entrez votre mot de passe Azure AD dans la zone de texte **Mot de passe**, puis cliquez sur le bouton **Connexion**.

    ![Test de l’authentification unique](./media/moconavi-tutorial/testing4.png)

7. L’authentification Azure AD est réussie lorsque le menu s’affiche.

    ![Test de l’authentification unique](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

