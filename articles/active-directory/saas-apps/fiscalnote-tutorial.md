---
title: 'Tutoriel : Intégration d’Azure Active Directory à FiscalNote | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42dd7b23416e723d9c51cc065e61cfd7a1b3aaa9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198828"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Tutoriel : Intégration d’Azure Active Directory à FiscalNote

Dans ce tutoriel, vous allez apprendre à intégrer FiscalNote dans Azure Active Directory (Azure AD).

L’intégration de FiscalNote dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à FiscalNote.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FiscalNote (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à FiscalNote, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FiscalNote pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FiscalNote depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-fiscalnote-from-the-gallery"></a>Ajout de FiscalNote depuis la galerie
Pour configurer l’intégration de FiscalNote à Azure AD, vous devez ajouter FiscalNote à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter FiscalNote à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **FiscalNote**, sélectionnez **FiscalNote** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![FiscalNote dans la liste des résultats](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec FiscalNote sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans FiscalNote équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur FiscalNote associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec FiscalNote, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test FiscalNote](#create-a-fiscalnote-test-user)** pour avoir un équivalent de Britta Simon dans FiscalNote, lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application FiscalNote.

**Pour configurer l’authentification unique Azure AD avec FiscalNote, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **FiscalNote**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

1. Dans la section **Domaine et URL FiscalNote**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique FiscalNote](mailto:support@fiscalnote.com).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

1. L’application FiscalNote s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications.

    ![Configurer l'authentification unique](./media/fiscalnote-tutorial/tutorial_attribute.png)

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
           
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| ----------------|
    | Nom | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | email| user.mail|
    
    a. Supprimez les attributs existants et ajoutez de nouveaux attributs. Cliquez sur **Ajouter un attribut** pour ouvrir la **boîte de dialogue** associée.

    ![Configurer l'authentification unique](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez l’espace de noms vide.
    
    e. Cliquez sur **OK**.

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/fiscalnote-tutorial/tutorial_general_400.png)

1. Dans la section **FiscalNote Configuration** (Configuration de FiscalNote), cliquez sur **Configure FiscalNote** (Configurer FiscalNote) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

1. Pour configurer l’authentification unique côté **FiscalNote**, vous devez envoyer le **Certificat (brut) téléchargé, l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à [l’équipe de support FiscalNote](mailto:support@fiscalnote.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/fiscalnote-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/fiscalnote-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/fiscalnote-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-fiscalnote-test-user"></a>Créer un utilisateur de test FiscalNote

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans FiscalNote. FiscalNote prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à FiscalNote s’il n’existe pas encore.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez  [l’équipe du support technique de FiscalNote](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FiscalNote.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à FiscalNote, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **FiscalNote**.

    ![Lien FiscalNote dans la liste des applications](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette FiscalNote dans le volet d’accès, vous devez être connecté automatiquement à votre application FiscalNote.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

