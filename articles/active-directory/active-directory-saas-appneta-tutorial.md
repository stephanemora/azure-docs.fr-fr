---
title: 'Didacticiel : intégration d’Azure Active Directory à AppNeta Performance Monitor | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 6c06b2819516664a367ca31925366eabd05e688e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Didacticiel : intégration d’Azure Active Directory à AppNeta Performance Monitor

Dans ce didacticiel, vous allez apprendre à intégrer AppNeta Performance Monitor à Azure Active Directory (Azure AD).

L’intégration d’AppNeta Performance Monitor à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à AppNeta Performance Monitor.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à AppNeta Performance Monitor (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à AppNeta Performance Monitor, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement AppNeta Performance Monitor pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’AppNeta Performance Monitor à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Ajout d’AppNeta Performance Monitor à partir de la galerie
Pour configurer l’intégration d’AppNeta Performance Monitor à Azure AD, vous devez ajouter AppNeta Performance Monitor, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter AppNeta Performance Monitor à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **AppNeta Performance Monitor**, sélectionnez **AppNeta Performance Monitor** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![AppNeta Performance Monitor dans la liste des résultats](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AppNeta Performance Monitor à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur AppNeta Performance Monitor équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur AppNeta Performance Monitor associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AppNeta Performance Monitor, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test AppNeta Performance Monitor](#create-an-appneta-performance-monitor-test-user)** pour disposer d’un équivalent de Britta Simon dans AppNeta Performance Monitor, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application AppNeta Performance Monitor.

**Pour configurer l’authentification unique Azure AD avec AppNeta Performance Monitor, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **AppNeta Performance Monitor**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_samlbase.png)

3. Dans la section **Domaine et URL AppNeta Performance Monitor**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL AppNeta Performance Monitor](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.pm.appneta.com`

    b. Dans la zone de texte **Identificateur**, entrez la valeur : `PingConnect`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique client d’AppNeta Performance Monitor](mailto:support@appneta.com). 

5. L’application AppNeta Performance Monitor s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications.

    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
           
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | Nom| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|

    > [!NOTE]
    > L’attribut « groups » désigne le groupe de sécurité dans Appneta qui est mappé sur un « Rôle » dans Azure AD. Reportez-vous à [ce document](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) expliquant comment créer des rôles personnalisés dans Azure AD.
        
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez l’espace de noms vide.
    
    e. Cliquez sur **OK**.  

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-appneta-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique du côté **AppNeta Performance Monitor**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe du support technique d’AppNeta Performance Monitor](mailto:support@appneta.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-appneta-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-appneta-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-appneta-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-appneta-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Créer un utilisateur de test AppNeta Performance Monitor

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans AppNeta Performance Monitor. AppNeta Performance Monitor prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à AppNeta Performance Monitor.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support client d’AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AppNeta Performance Monitor.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à AppNeta Performance Monitor, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **AppNeta Performance Monitor**.

    ![Lien AppNeta Performance Monitor dans la liste des applications](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette AppNeta Performance Monitor dans le panneau d’accès, vous devriez être connecté automatiquement à votre application AppNeta Performance Monitor.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_203.png

