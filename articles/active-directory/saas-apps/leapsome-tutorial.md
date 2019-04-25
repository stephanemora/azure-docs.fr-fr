---
title: 'Didacticiel : Intégration d’Azure Active Directory à Leapsome | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260625"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Didacticiel : Intégration d’Azure Active Directory à Leapsome

Dans ce tutoriel, vous allez apprendre à intégrer Leapsome à Azure Active Directory (Azure AD).

L’intégration d’Azure AD à Leapsome offre les avantages suivants :

- Dans Azure D, vous pouvez contrôler qui a accès à Leapsome.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Leapsome (via l’authentification unique) avec leur compte Azure D.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD dans Leapsome, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Leapsome pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Leapsome à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-leapsome-from-the-gallery"></a>Ajout de Leapsome à partir de la galerie
Pour configurer l’intégration de Leapsome à Azure AD, vous devez ajouter Leapsome à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Leapsome à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Leapsome**, sélectionnez **Leapsome** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Leapsome dans la liste des résultats](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Leapsome, à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Leapsome équivalent dans Azure AD. En d’autres termes, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Leapsome qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Leapsome, vous devez effectuer ce qui suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Leapsome](#create-a-leapsome-test-user)** pour avoir un équivalent de Britta Simon dans Leapsome qui soit lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Leapsome.

**Pour configurer l’authentification unique Azure AD avec Leapsome, effectuez les étapes suivantes :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Leapsome**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. Dans la section **Domaines et URL Leapsome**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Leapsome](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://www.leapsome.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Leapsome](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Les valeurs URL de réponse et URL de connexion ci-dessus ne sont pas de vraies valeurs. Vous devrez les remplacer par les valeurs réelles. La procédure est expliquée plus loin dans le tutoriel.

1. L’application Leapsome s’attend à ce que les assertions SAML soient dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante présente un exemple.
    
    ![Configurer l'authentification unique](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut | Espace de noms |
    | ---------------| --------------- | --------- |   
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | URL de la photo de l’employé | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > La valeur de l’attribut picture n’est pas réelle. Pour cette valeur, fournissez la vraie URL de photo. Pour obtenir cette valeur, contactez [l’équipe du support technique de Leapsome](mailto:support@leapsome.com).
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Dans la zone de texte **Espace de noms**, tapez l’URI de l’espace de noms pour cette ligne.
    
    e. Cliquez sur **OK**.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. Dans la section **Leapsome Configuration** (Configuration de Leapsome), cliquez sur **Configure Leapsome** (Configurer Leapsome) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous à Leapsome en tant qu’administrateur de la sécurité.

1. Dans le coin supérieur droit, cliquez sur le logo Paramètres, puis cliquez sur **Admin Settings** (Paramètres d’administration). 

    ![Leapsome configuré](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Dans la barre de menu de gauche, cliquez sur **Single Sign On (SSO)** (Authentification unique), puis, dans la page **SAML-based single sign-on (SSO)** (Authentification unique SAML), procédez aux étapes suivantes :
    
    ![SAML Leapsome](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Sélectionnez **Enable SAML-based single sign-on** (Activer l’authentification unique SAML).

    b. Copiez la valeur de **Login URL (point your users here to start login)** (URL de connexion (à indiquer aux utilisateurs pour démarrer la connexion)), puis collez-la dans la zone de texte **Sign-on URL** (URL de connexion) de la section **Leapsome Domain and URLs** (Domaine et URL Leapsome) du portail Azure.

    c. Copiez la valeur de **Reply URL (receives response from your identity provider)** (URL de réponse [réponse reçue du fournisseur d’identité]), puis collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Leapsome** du portail Azure.

    d. Dans la zone de texte **SSO Login URL (provided by identity provider)** (URL de connexion SSO - fournie par le fournisseur d'identité), collez la valeur de **l’URL du service d'authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Copiez le certificat que vous avez téléchargé à partir du portail Azure sans les commentaires --BEGIN CERTIFICATE et END CERTIFICATE--, puis collez-le dans la zone de texte **Certificate (provided by identity provider)** (Certificat - fourni par le fournisseur d'identité).

    f. Cliquez sur **Update SSO Settings** (Mettre à jour les paramètres SSO).
    
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/leapsome-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/leapsome-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/leapsome-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-leapsome-test-user"></a>Créer un utilisateur de test Leapsome

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Leapsome. Collaborez avec [l’équipe du support technique de Leapsome](mailto:support@leapsome.com) pour ajouter les utilisateurs ou le domaine devant être mis en liste verte dans la plateforme Leapsome. Si le domaine est ajouté par l’équipe, les utilisateurs sont automatiquement provisionnés dans la plateforme Leapsome. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Leapsome.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Leapsome, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Leapsome**.

    ![Lien Leapsome dans la liste des applications](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Leapsome dans le volet d’accès, vous êtes automatiquement connecté à votre application Leapsome.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

