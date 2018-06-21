---
title: 'Tutoriel : Intégration d’Azure Active Directory à Arc Publishing - Authentification unique | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Arc Publishing - Authentification unique.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: bf811d789c0c6effd6f8940ad433092ea9ba04cb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210069"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Tutoriel : Intégration d’Azure Active Directory à Arc Publishing - Authentification unique

Dans ce tutoriel, vous allez apprendre à intégrer Arc Publishing - Authentification unique à Azure Active Directory (Azure AD).

L’intégration d’Arc Publishing - Authentification unique à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Arc Publishing - Authentification unique.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Arc Publishing - Authentification unique (SSO) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Arc Publishing - Authentification unique, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Arc Publishing - Authentification unique activé

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Arc Publishing - Authentification unique à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Ajout d’Arc Publishing - Authentification unique à partir de la galerie
Pour configurer l’intégration d’Arc Publishing - Authentification unique à Azure AD, vous devez ajouter Arc Publishing - Authentification unique à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Arc Publishing - Authentification unique à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Arc Publishing - Authentification unique**, sélectionnez **Arc Publishing - Authentification unique** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Arc Publishing - Authentification unique dans la liste des résultats](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’Arc Publishing - Authentification unique avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Arc Publishing - Authentification unique équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Arc Publishing - Authentification unique associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Arc Publishing - Authentification unique, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Arc Publishing - Authentification unique](#create-an-arc-publishing---sso-test-user)** pour avoir un équivalent de Britta Simon dans Arc Publishing - Authentification unique lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Arc Publishing - Authentification unique.

**Pour configurer l’authentification unique Azure AD auprès d’Arc Publishing - Authentification unique, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Arc Publishing - Authentification unique**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/arc-tutorial/tutorial_arc_samlbase.png)

3. Dans la section **Domaine et URL Arc Publishing - Authentification unique**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP** :

    ![Informations d’authentification unique dans Domaine et URL Arc Publishing - Authentification unique](./media/arc-tutorial/tutorial_arc_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Arc Publishing - Authentification unique](./media/arc-tutorial/tutorial_arc_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Arc Publishing - Authentification unique](mailto:inf@washpost.com). 

5. L’application Arc Publishing - Authentification unique attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/arc-tutorial/tutorial_arc_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | groups | user.assignedroles |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

     ![Configure Single Sign-On](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Configure Single Sign-On](./media/arc-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    d. Cliquez sur **OK**.

    > [!NOTE]
    > Ici, l’attribut **groups** est mappé sur **user.assignedroles**. Il existe des rôles personnalisés créés dans Azure AD pour remapper les noms de groupe dans l’application. Vous trouverez plus d’informations [ici](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) sur la façon de créer des rôles personnalisés dans Azure AD. 

7. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/arc-tutorial/tutorial_arc_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/arc-tutorial/tutorial_general_400.png)
    
9. Dans la section **Configuration d’Arc Publishing - Authentification unique**, cliquez sur **Configurer Arc Publishing - Authentification unique** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration d’Arc Publishing - Authentification unique](./media/arc-tutorial/tutorial_arc_configure.png) 

10. Pour configurer l’authentification unique côté **Arc Publishing - Authentification unique**, vous devez envoyer le **certificat (Base64) téléchargé, l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à l’[équipe de support technique Arc Publishing - Authentification unique](mailto:inf@washpost.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/arc-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/arc-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/arc-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/arc-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Créer un utilisateur Arc Publishing - Authentification unique

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Arc Publishing - Authentification unique. Arc Publishing - Authentification unique prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un nouvel utilisateur est créé durant une tentative d’accès à Arc Publishing - Authentification unique.

>[!Note]
>Si vous avez besoin créer un utilisateur manuellement, contactez [l’équipe de support technique Arc Publishing - Authentification unique](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Arc Publishing - Authentification unique.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Arc Publishing - Authentification unique, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Arc Publishing - Authentification unique**.

    ![Lien Arc Publishing - Authentification unique dans la liste des applications](./media/arc-tutorial/tutorial_arc_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Arc Publishing - Authentification unique dans le volet d’accès, vous devez être connecté automatiquement à votre application Arc Publishing - Authentification unique.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

