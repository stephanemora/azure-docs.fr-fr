---
title: 'Tutoriel : Intégration d’Azure Active Directory à Shuccho Navi | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Shuccho Navi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32b6676c-d1ec-48c2-91b1-41990ed0560c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1709abdf1d5e90cb253e67b29c0e48f6aa29a712
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209674"
---
# <a name="tutorial-azure-active-directory-integration-with-shuccho-navi"></a>Didacticiel : Intégration d’Azure Active Directory à Shuccho Navi

Dans ce tutoriel, vous allez apprendre à intégrer Shuccho Navi à Azure Active Directory (Azure AD).

L’intégration de Shuccho Navi à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Shuccho Navi.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Shuccho Navi (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Shuccho Navi, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Shuccho Navi pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Shuccho Navi depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-shuccho-navi-from-the-gallery"></a>Ajout de Shuccho Navi depuis la galerie
Pour configurer l’intégration de Shuccho Navi à Azure AD, vous devez ajouter Shuccho Navi, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Shuccho Navi à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/shucchonavi-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/shucchonavi-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/shucchonavi-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Shuccho Navi**, sélectionnez **Shuccho Navi** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Shuccho Navi sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Shuccho Navi équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Shuccho Navi associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Shuccho Navi, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Shuccho Navi](#create-a-shuccho-navi-test-user)** pour avoir un équivalent de Britta Simon dans Shuccho Navi lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Shuccho Navi.

**Pour configurer l’authentification unique Azure AD avec Shuccho Navi, effectuez les étapes suivantes :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Shuccho Navi**, sélectionnez **Authentification unique**.

    ![image](./media/shucchonavi-tutorial/b1_b2_select_sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/shucchonavi-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/shucchonavi-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://naviauth.nta.co.jp/saml/login?ENTP_CD=<Your company code>`.

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_url.png)

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe du support technique Shuccho Navi](mailto:sys_ntabtm@nta.co.jp) pour obtenir la valeur.
 
5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **XML de métadonnées de fédération**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_certificate.png) 

6. Pour configurer l’authentification unique côté **Shuccho Navi**, vous devez envoyer le **XML de métadonnées de fédération** téléchargé à [l’équipe du support technique Shuccho Navi](mailto:sys_ntabtm@nta.co.jp). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/shucchonavi-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/shucchonavi-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/shucchonavi-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-shuccho-navi-test-user"></a>Créer un utilisateur de test Shuccho Navi

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Shuccho Navi. Contactez  [l’équipe du support technique Shuccho Navi](mailto:sys_ntabtm@nta.co.jp) pour ajouter les utilisateurs dans la plateforme Shuccho Navi. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Shuccho Navi.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/shucchonavi-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Shuccho Navi**.

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/shucchonavi-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/shucchonavi-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Shuccho Navi dans le panneau d’accès, vous devez être connecté automatiquement à votre application Shuccho Navi.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


