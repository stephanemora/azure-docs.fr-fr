---
title: 'Tutoriel : Intégration d’Azure Active Directory à Adaptive Insights | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884878"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Didacticiel : Intégration d’Azure Active Directory à Adaptive Insights

Dans ce didacticiel, vous allez apprendre à intégrer Adaptive Insights dans Azure Active Directory (Azure AD).

L’intégration d’Adaptive Insights dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adaptive Insights.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adaptive Insights (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans Adaptive Insights, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adaptive Insights pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adaptive Insights à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adaptive-insights-from-the-gallery"></a>Ajout d’Adaptive Insights à partir de la galerie
Pour configurer l’intégration d’Adaptive Insights dans Azure AD, vous devez ajouter Adaptive Insights, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Adaptive Insights à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Adaptive Insights**, sélectionnez **Adaptive Insights** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adaptive Insights, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adaptive Insights correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adaptive Insights associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Adaptive Insights, vous devez suivre les indications des sections ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test Adaptive Insights](#create-an-adaptive-insights-test-user)** pour avoir un équivalent de Britta Simon dans Adaptive Insights lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Adaptive Insights.

**Pour configurer l’authentification unique Azure AD avec Adaptive Insights, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Adaptive Insights**, cliquez sur **Authentification unique**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Vous pouvez obtenir les valeurs de l’Identificateur (ID d’entité) et l’URL de réponse sur la page **Paramètres d’authentification unique SAML** d’Adaptive Insights.
 
5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Dans la section **Configurer Adaptive Insights**, copiez l’URL appropriée en fonction de vos besoins.

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adaptive Insights en tant qu’administrateur.

8. Accédez à **Admin**.

    ![Administrateur](./media/adaptivesuite-tutorial/IC805644.png "Administrateur")

9. Dans la section **Users and Roles**, cliquez sur **Manage SAML SSO Settings**.

    ![Gérer les paramètres d’authentification unique de SAML](./media/adaptivesuite-tutorial/IC805645.png "Gérer les paramètres d’authentification unique de SAML")

10. Dans la page **SAML SSO Settings** , procédez comme suit :

    ![Paramètres d’authentification unique de SAML](./media/adaptivesuite-tutorial/IC805646.png "Paramètres d’authentification unique de SAML")

    a. Dans la zone de texte **Identity provider name** , attribuez un nom à votre configuration.

    b. Collez la valeur **Identificateur Azure AD** copiée à partir du portail Azure dans la zone de texte **ID d’entité du fournisseur d’identité**.

    c. Collez la valeur **URL de connexion** copiée à partir du portail Azure dans la zone de texte **URL SSO du fournisseur d’identité**.

    d. Collez la valeur **URL de déconnexion** copiée à partir du portail Azure dans la zone de texte **URL de déconnexion personnalisée**.

    e. Pour charger votre certificat téléchargé, cliquez sur **Choisir un fichier**.

    f. Sélectionnez les options suivantes :

    * Pour **SAML user id**, sélectionnez **User’s Adaptive Insights user name**.

    * Pour **SAML user id location**, sélectionnez **User id in NameID of Subject**.

    * Pour **SAML NameID format**, sélectionnez **Email address**.

    * Pour **Enable SAML**, sélectionnez **Allow SAML SSO and direct Adaptive Insights login**.

    g. Copiez l’**URL d’authentification unique Adaptive Insights** et collez-la dans les zones de texte **Identificateur (ID d’entité)** et **URL de réponse** dans la section **URL et domaine Adaptive Insights** dans le portail Azure.

    h. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le **nom d’utilisateur** type de champ **brittasimon\@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Créer un utilisateur de test Adaptive Insights

Pour permettre aux utilisateurs Azure AD de se connecter à Adaptive Insights, vous devez les approvisionner dans Adaptive Insights. Dans le cas d’Adaptive Insights, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :** 

1. Connectez-vous à votre site d’entreprise **Adaptive Insights** en tant qu’administrateur.
2. Accédez à **Admin**.

   ![Administrateur](./media/adaptivesuite-tutorial/IC805644.png "Administrateur")

3. Dans la section **Users and Roles**, cliquez sur **Add User**.

   ![Ajouter un utilisateur](./media/adaptivesuite-tutorial/IC805648.png "Ajouter un utilisateur")
   
4. Dans la section **New User**, procédez comme suit :

   ![Envoyer](./media/adaptivesuite-tutorial/IC805649.png "Envoyer")

   a. Tapez le nom, l’identifiant de connexion, l’adresse de messagerie et le mot de passe de l’utilisateur Azure Active Directory valide que vous souhaitez renseigner dans les zones de texte correspondantes, à savoir, **Name**, **Login**, **Email** et **Password**.

   b. Sélectionnez un **rôle**.

   c. Cliquez sur **Envoyer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Adaptive Insights fourni par ce service pour approvisionner des comptes d’utilisateur Azure Active Directory.
>

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adaptive Insights.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Adaptive Insights**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Adaptive Insights dans le panneau d’accès, vous devez être connecté automatiquement à votre application Adaptive Insights.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
