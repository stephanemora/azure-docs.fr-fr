---
title: 'Tutoriel : Intégration d’Azure Active Directory à Zoho One | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aadb3188969e099a709711ac18916b67c83b5da1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884436"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutoriel : Intégration d’Azure Active Directory à Zoho One

Dans ce tutoriel, vous allez apprendre à intégrer Zoho One dans Azure Active Directory (Azure AD).

L’intégration de Zoho One dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zoho One.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Zoho One (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Zoho One, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zoho One pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zoho One à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zoho-one-from-the-gallery"></a>Ajout de Zoho One à partir de la galerie
Pour configurer l’intégration de Zoho One avec Azure AD, vous devez ajouter Zoho One, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zoho One à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Zoho One**, sélectionnez **Zoho One** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zoho One dans la liste des résultats](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zoho One avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zoho One équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Zoho One associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Zoho One, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Zoho One](#create-a-zoho-one-test-user)** pour avoir dans Zoho One un équivalent de Britta Simon qui soit associé à la représentation de l’utilisateur Azure AD.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Zoho One.

**Pour configurer l’authentification unique Azure AD avec Zoho One, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Zoho One**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Dans la section **Domaines et URL Zoho One**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Zoho One](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. Dans la zone de texte **Identificateur (ID d’entité)**, tapez une URL : `one.zoho.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Cliquez sur **Afficher les paramètres d’URL avancés**.

    d. Dans la zone de texte **État de relais**, tapez une URL : `https://one.zoho.com`

1. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Les valeurs **URL de réponse** et **URL de connexion**ci-dessus ne sont pas des valeurs réelles. Vous mettrez à jour la valeur avec l’URL de réponse et l’URL de connexion réelles. La procédure est expliquée plus loin dans le tutoriel. 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration de Zoho One**, cliquez sur **Configurer Zoho One** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Zoho One](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoho One en tant qu’administrateur.

1. Sous l’onglet **Organisation**, cliquez sur **Configurer** sous **Authentification SAML**.

    ![Organisation Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. Dans la page qui s’affiche, effectuez les étapes suivantes :

    ![Connexion à Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Dans la zone de texte **URL d’authentification unique**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **URL de déconnexio**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **Parcourir** pour charger le certificat (**Certificat (Base64)**) que vous avez téléchargé à partir du portail Azure.

    d. Cliquez sur **Enregistrer**.

1. Après avoir enregistré la configuration de l’authentification SAML, copiez la valeur **SAML-Identifier** et collez-la dans la zone **URL de réponse** dans le portail Azure, dans la section **Domaines et URL Zoho One**.

    ![SAML Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Accédez à l’onglet **Domains**, puis cliquez sur **Ajouter un domaine**.

    ![Domaine Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Dans la page **Ajouter un utilisateur**, effectuez les étapes suivantes :

    ![Ajouter un domaine Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Dans la zone de texte **Nom de domaine**, tapez un domaine comme **contoso.com**.

    b. Cliquez sur **Add**.

    >[!Note]
    >Après avoir ajouté le domaine, effectuez [ces](https://www.zoho.com/one/help/admin-guide/domain-verification.html) étapes pour vérifier votre domaine. Une fois le domaine vérifié, tapez le nom de votre domaine dans **URL de connexion** dans la section **Domaines et URL Zoho One** du portail Azure.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/zohoone-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/zohoone-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/zohoone-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-zoho-one-test-user"></a>Créer un utilisateur de test Zoho One

Pour que les utilisateurs Azure AD puissent se connecter à Zoho One, ils doivent être attribués dans Zoho One. Dans Zoho One, l’attribution est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Zoho One en tant qu’administrateur de la sécurité.

1. Sous l’onglet **Utilisateur**, cliquez sur **logo de l’utilisateur**.

    ![Utilisateur Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Dans la page **Add user** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Dans la zone de texte **Nom**, entrez le nom d’un utilisateur, par exemple **Britta Simon**.
    
    b. Dans **adresse de messagerie** texte, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    >[!Note]
    >Sélectionnez votre domaine vérifié dans la liste des domaines.

    c. Cliquez sur **Add**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zoho One.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Zoho One, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Zoho One**.

    ![Lien Zoho One dans la liste des applications](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Zoho One dans le volet d’accès, vous devez être connecté automatiquement à votre application Zoho One.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

