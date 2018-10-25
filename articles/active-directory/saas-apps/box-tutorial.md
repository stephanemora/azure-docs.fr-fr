---
title: "Tutorial: Intégration d'Azure Active Directory à Box | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 535f59b1b0dc56b183c8a019d101b4fd4f1bfad6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116123"
---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Didacticiel : Intégration d'Azure Active Directory à Box

Dans ce didacticiel, vous allez apprendre à intégrer Box dans Azure Active Directory (Azure AD).

L’intégration de Box dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Box.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Box (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Box, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Box pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Box à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-box-from-the-gallery"></a>Ajout de Box à partir de la galerie
Pour configurer l’intégration de Box à Azure AD, vous devez ajouter Box, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Box à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/box-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/box-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/box-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Box**, sélectionnez **Box** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/box-tutorial/tutorial_Box_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Box, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Box correspondant dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Box associé.

Pour configurer et tester l’authentification unique Azure AD avec Box, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Box](#create-a-box-test-user)** pour avoir un équivalent de Britta Simon dans Box lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Box.

**Pour configurer l’authentification unique Azure AD avec Box, effectuez les étapes suivantes :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Box**, cliquez sur **Authentification unique**.

    ![image](./media/box-tutorial/b1_b2_select_sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/box-tutorial/b1_b2_saml_sso.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/box-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.account.box.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL :`box.net`

    ![image](./media/box-tutorial/tutorial_box_url.png)

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) pour obtenir la valeur.
 
5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **XML de métadonnées de fédération**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/box-tutorial/tutorial_Box_certificate.png)

6. Pour configurer l’authentification unique pour votre application, suivez la procédure décrite dans [Configurer vous-même l’authentification unique](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown). 

>[!NOTE]
>Si vous ne pouvez pas configurer les paramètres de l’authentification unique pour votre compte Box, vous devez envoyer le **XML de métadonnées de fédération** à [l’équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/box-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/box-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/box-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-box-test-user"></a>Créer un utilisateur de test Box

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Box. Box prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé durant une tentative d’accès à Box s’il n’existe pas déjà.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Box.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/box-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Box**.

    ![image](./media/box-tutorial/tutorial_Box_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/box-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/box-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette **Box** dans le volet d’accès, vous ouvrez la page de connexion pour vous connecter à votre application Box.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement des utilisateurs](box-userprovisioning-tutorial.md)


