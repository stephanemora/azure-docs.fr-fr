---
title: 'Didacticiel : Intégration d’Azure Active Directory à MyWorkDrive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094054"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Didacticiel : Intégration d’Azure Active Directory à MyWorkDrive

Dans ce didacticiel, vous allez apprendre à intégrer MyWorkDrive à Azure Active Directory (Azure AD).

L’intégration de MyWorkDrive à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MyWorkDrive.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à MyWorkDrive (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à MyWorkDrive, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MyWorkDrive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MyWorkDrive à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-myworkdrive-from-the-gallery"></a>Ajout de MyWorkDrive à partir de la galerie
Pour configurer l’intégration de MyWorkDrive à Azure AD, vous devez ajouter MyWorkDrive à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter MyWorkDrive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/myworkdrive-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/myworkdrive-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **MyWorkDrive**, sélectionnez **MyWorkDrive** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MyWorkDrive grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur MyWorkDrive correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur MyWorkDrive associé.

Pour configurer et tester l’authentification unique Azure AD avec MyWorkDrive, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test MyWorkDrive](#create-a-myworkdrive-test-user)** pour avoir un équivalent de Britta Simon dans MyWorkDrive lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application MyWorkDrive.

**Pour configurer l’authentification unique Azure AD avec MyWorkDrive, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **MyWorkDrive**, sélectionnez **Authentification unique**.

    ![image](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles.  Entrez le nom d’hôte du serveur MyWorkDrive de votre propre société : p. ex.
    > 
    > URL de réponse : `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de connexion :`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contactez l’équipe de support client MyWorkDrive si vous avez des doutes sur la façon de configurer vos propres nom d’hôte et certificat SSL pour ces valeurs.

6. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’icône **Copier** pour copier l’**URL des métadonnées de fédération d’application** et cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** et l’enregistrer sur votre ordinateur.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. Dans la section **Configurer MyWorkDrive**, copiez l’URL appropriée en fonction de vos besoins.

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![image](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Pour configurer l’authentification unique côté MyWorkDrive, téléchargez les éléments **Certificat (Base64), URL de déconnexion, ID d’entité SAML et URL du service d’authentification unique SAML**, et configurez-les manuellement sur le serveur MyWorkDrive ou copiez et collez l’**URL des métadonnées de fédération d’application** Azure dans votre écran de configuration Azure AD SAML du volet d’administration du serveur MyWorkDrive. Pour plus d’informations, contactez l’[équipe du support technique MyWorkDrive](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/myworkdrive-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/myworkdrive-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-myworkdrive-test-user"></a>Créer un utilisateur de test MyWorkDrive

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MyWorkDrive. Collaborez avec l’ [équipe du support technique MyWorkDrive](mailto:support@myworkdrive.com) pour ajouter les utilisateurs dans la plateforme MyWorkDrive. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MyWorkDrive.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/myworkdrive-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **MyWorkDrive**.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/myworkdrive-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette MyWorkDrive dans le volet d’accès, vous devez être connecté automatiquement à votre application MyWorkDrive.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
