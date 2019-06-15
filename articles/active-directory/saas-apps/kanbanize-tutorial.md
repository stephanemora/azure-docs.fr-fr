---
title: "Didacticiel : Intégration d'Azure Active Directory à Kanbanize | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60263727"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Didacticiel : Intégration d'Azure Active Directory à Kanbanize

Dans ce tutoriel, vous allez apprendre à intégrer Kanbanize à Azure Active Directory (Azure AD).

L’intégration de Kanbanize à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Kanbanize.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Kanbanize (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Kanbanize, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kanbanize pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kanbanize à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kanbanize-from-the-gallery"></a>Ajout de Kanbanize à partir de la galerie
Pour configurer l’intégration de Kanbanize à Azure AD, vous devez ajouter Kanbanize à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Kanbanize à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Kanbanize**, sélectionnez **Kanbanize** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Kanbanize dans la liste des résultats](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD dans Kanbanize avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Kanbanize équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Kanbanize qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Kanbanize, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Kanbanize](#create-a-kanbanize-test-user)** pour avoir un équivalent de Britta Simon dans Kanbanize qui soit lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kanbanize.

**Pour configurer l’authentification unique Azure AD avec Kanbanize, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Kanbanize**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Dans la section **Domaines et URL Kanbanize**, suivez les étapes ci-dessous pour configurer l’application en mode initié par le **fournisseur d’identité** :

    ![Informations d’authentification unique dans Domaine et URL Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.kanbanize.com/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.kanbanize.com/saml/acs`

    c. Cliquez sur **Afficher les paramètres d’URL avancés**.

    d.  Dans la zone de texte **État de relais**, tapez une URL : `/ctrl_login/saml_login`

    e. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de services**, dans la zone de texte **URL d’authentification**, tapez une URL au format suivant : `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique Kanbanize](mailto:support@ms.kanbanize.com). 

5. Votre application Kanbanize s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais Kanbanize s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation
    
    ![Configurer l'authentification unique](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Dans la section **Configuration de Kanbanize**, cliquez sur **Configurer Kanbanize** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Kanbanize en tant qu’administrateur de la sécurité. 

10. En haut à droite, cliquez sur l’icône des **paramètres**.

    ![Paramètres Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Dans la page du volet Administration, à gauche du menu, cliquez sur **Integrations**, puis activez l’option **Single Sign-On** (Authentification unique). 

    ![Intégrations Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Dans la section Integrations, cliquez sur **CONFIGURE** (Configurer) pour ouvrir la page **Single Sign-On Integration** (Intégration de l’authentification unique).

    ![Configuration de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Dans la page **Single Sign-On Configuration** (Configuration de l’authentification unique), sous **Configurations**, effectuez les étapes suivantes :

    ![Intégrations Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. Dans la zone de texte **IdP Entity ID** (ID d’entité du fournisseur d’identité), collez la valeur **SAML Entity ID** (ID d’entité SAML) que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **IdP Login Endpoint** (Point de terminaison de connexion du fournisseur d’identité), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **IdP Logout Endpoint** (Point de terminaison de déconnexion du fournisseur d’identité), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Attribute name for Email** (Nom d’attribut pour l’e-mail), entrez cette valeur : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Dans la zone de texte **Attribute name for First Name** (Nom d’attribut pour le prénom), entrez cette valeur : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Dans la zone de texte **Attribute name for Last Name** (Nom d’attribut pour le nom de famille), entrez cette valeur : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Vous pouvez obtenir ces valeurs en associant les valeurs de l’espace de noms et du nom de l’attribut, situées dans la section Attributs utilisateur du portail Azure.

    g. Dans le Bloc-notes, ouvrez le certificat codé en base 64 que vous avez téléchargé sur le portail Azure, copiez son contenu (sans les marques de début et de fin), puis collez-le dans la zone  **Certificat X.509 du fournisseur d'identité** .

    h. Cochez l’option **Enable login with both SSO and Kanbanize** (Permettre la connexion avec SSO et Kanbanize).
    
    i. Cliquez sur **Save Settings**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-kanbanize-test-user"></a>Créer un utilisateur de test Kanbanize

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Kanbanize. Kanbanize prend en charge le provisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Lorsque vous tentez d’accéder à Kanbanize, si aucun utilisateur n’existe, Kanbanize en crée un automatiquement.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l' [équipe du support technique de Kanbanize](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kanbanize.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Kanbanize, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Kanbanize**.

    ![Lien Kanbanize dans la liste des applications](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette Kanbanize dans le volet d’accès, vous devez vous connecter automatiquement à votre application Kanbanize.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

