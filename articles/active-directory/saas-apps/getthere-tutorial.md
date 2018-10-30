---
title: 'Didacticiel : Intégration d’Azure Active Directory avec GetThere | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432595"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Didacticiel : Intégration d’Azure Active Directory à GetThere

Dans ce didacticiel, vous allez apprendre à intégrer GetThere à Azure Active Directory (Azure AD).

L’intégration de GetThere dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à GetThere.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à GetThere (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à GetThere, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique GetThere est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de GetThere depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-getthere-from-the-gallery"></a>Ajout de GetThere depuis la galerie
Pour configurer l’intégration de GetThere à Azure AD, vous devez ajouter GetThere à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter GetThere à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/getthere-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **GetThere**, sélectionnez **GetThere** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec GetThere avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur GetThere équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur GetThere associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec GetThere, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test GetThere](#create-a-getthere-test-user)** pour avoir un équivalent de Britta Simon dans GetThere lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application GetThere.

**Pour configurer l’authentification unique Azure AD avec GetThere, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **GetThere**, sélectionnez **Authentification unique**.

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL ci-dessous :
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL ci-dessous :
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. L’application GetThere attend la valeur **Username** unique dans la revendication de nom d’utilisateur. Le client peut mapper la valeur correcte pour la revendication Nom d’utilisateur. Dans l’exemple ci-dessous, nous avons mappé le **Nom d’utilisateur**  avec **user.mail**, mais vous pouvez modifier le mappage en fonction des paramètres de votre organisation. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | NOM |  Attribut source |  Espace de noms |
    | ---------------| --------------- | --------------- |
    | SiteName | « Indiquez la valeur conformément à votre organisation » | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Nom d’utilisateur |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. Dans la section **Configurer GetThere**, copiez l’URL appropriée en fonction de vos besoins.

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL de connexion

    b. Identificateur Azure Active Directory

    c. URL de déconnexion

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. Pour configurer l’authentification unique côté **GetThere**, vous devez envoyer le **Certificat (Base64)** téléchargé et les **URL de connexion, Identificateur Azure AD et URL de déconnexion** à l’[équipe de support technique Snowflake](mailto:dataintegration@sabre.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/getthere-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-getthere-test-user"></a>Créer un utilisateur de test GetThere

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans GetThere. Collaborez avec l’[équipe du support du client GetThere](mailto:dataintegration@sabre.com) pour ajouter des utilisateurs dans la plateforme GetThere. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GetThere.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **GetThere**.

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque GetThere dans le volet d’accès, vous devez vous connecter automatiquement à votre application GetThere.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
