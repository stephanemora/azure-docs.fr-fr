---
title: "Didacticiel : Intégration d'Azure Active Directory à My Award Points Top Sub/Top Team | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et My Award Points Top Sub/Top Team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ed1c12768eed0265f65388984a5859b8f0630b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210728"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Tutoriel : Intégration d'Azure Active Directory à My Award Points Top Sub/Top Team

Dans ce didacticiel, vous allez apprendre à intégrer My Award Points Top Sub/Top Team à Azure Active Directory (Azure AD).

L’intégration de My Award Points Top Sub/Top Team à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à My Award Points Top Sub/Top Team.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à My Award Points Top Sub/Top Team (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à My Award Points Top Sub/Top Team, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement My Award Points Top Sub/Top Team pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de My Award Points Top Sub/Top Team à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Ajout de My Award Points Top Sub/Top Team à partir de la galerie

Pour configurer l’intégration de My Award Points Top Sub/Top Team à Azure AD, vous devez ajouter My Award Points Top Sub/Top Team à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter My Award Points Top Sub/Top Team à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **My Award Points Top Sub/Top Team**, sélectionnez **My Award Points Top Sub/Top Team** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![My Award Points Top Sub/Top Team dans la liste des résultats](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec My Award Points Top Sub/Top Team grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur My Award Points Top Sub/Top Team correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur My Award Points Top Sub/Top Team associé.

Pour configurer et tester l’authentification unique Azure AD avec My Award Points Top Sub/Top Team, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test My Award Points Top Sub/Top Team](#create-a-my-award-points-top-subtop-team-test-user)** pour avoir un équivalent de Britta Simon dans My Award Points Top Sub/Top Team lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application My Award Points Top Sub/Top Team.

**Pour configurer l’authentification unique Azure AD avec My Award Points Top Sub/Top Team, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **My Award Points Top Sub/Top Team**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. Dans la section **Domaine et URL My Award Points Top Sub/Top Team**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL My Award Points Top Sub/Top Team](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Vous obtiendrez la valeur `<SAMLENTITYID>` lors des étapes ultérieures de ce didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. Dans la section de configuration **My Award Points Top Sub/Top Team**, sélectionnez l’option **Configurer** pour ouvrir la fenêtre de configuration de l’authentification. Copiez l’ID d’entité SAML de la section **Aide-mémoire** et ajoutez à cette valeur d’URL d’authentification unique au lieu de `<SAMLENTITYID>` dans la section du domaine et des URL **My Award Points Top Sub/Top Team** dans le portail Azure.

7. Pour configurer l’authentification unique du côté **My Award Points Top Sub/Top Team**, vous devez envoyer le fichier **XML des métadonnées** téléchargé à [l’équipe du support technique de My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Créer un utilisateur de test My Award Points Top Sub/Top Team

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans My Award Points Top Sub/Top Team. Travaillez en collaboration avec l' [équipe du support technique de My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com)  pour ajouter des utilisateurs à la plateforme My Award Points Top Sub/Top Team. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à My Award Points Top Sub/Top Team.

![Attribuer le rôle utilisateur][200]

**Pour attribuer Britta Simon à My Award Points Top Sub/Top Team, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **My Award Points Top Sub/Top Team**.

    ![Lien My Award Points Top Sub/Top Team dans la liste des applications](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette My Award Points Top Sub/Top Team dans le volet d’accès, vous devez être connecté automatiquement à votre application My Award Points Top Sub/Top Team.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png
