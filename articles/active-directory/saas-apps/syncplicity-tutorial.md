---
title: 'Tutoriel : Intégration d’Azure Active Directory à Syncplicity | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f6fcc4d2920841c730ef179497f9184b1f6649d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451950"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutoriel : Intégration d’Azure Active Directory à Syncplicity

Dans ce didacticiel, vous allez apprendre à intégrer Syncplicity à Azure Active Directory (Azure AD).

L’intégration de Syncplicity dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Syncplicity.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Syncplicity (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Syncplicity, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Syncplicity pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Syncplicity à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-syncplicity-from-the-gallery"></a>Ajout de Syncplicity à partir de la galerie
Pour configurer l’intégration de Syncplicity à Azure AD, vous devez ajouter Syncplicity à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Syncplicity à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Syncplicity**.

    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. Dans le panneau de résultats, sélectionnez **Syncplicity**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Syncplicity à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Syncplicity équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Syncplicity associé doit être établie.

Dans Syncplicity, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Syncplicity, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Syncplicity](#creating-a-syncplicity-test-user)** pour avoir un équivalent de Britta Simon dans Syncplicity lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Syncplicity.

**Pour configurer l’authentification unique Azure AD avec Syncplicity, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Syncplicity**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Dans la section **Syncplicity Domain and URLs** (Domaine et URL Syncplicity), procédez comme suit :

    ![Configurer l'authentification unique](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.syncplicity.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Syncplicity](https://www.syncplicity.com/contact-us). 
 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Dans la section **Syncplicity Configuration** (Configuration de Syncplicity), cliquez sur **Configure Syncplicity** (Configurer Syncplicity) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Connectez-vous à votre locataire **Syncplicity** .

1. Dans le menu en haut, cliquez sur **admin**, sélectionnez **settings**, puis cliquez sur **Custom domain and single sign-on**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Dans la boîte de dialogue **Single Sign on (SSO)** , procédez comme suit :
   
    ![Authentification unique \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. Dans la zone de texte **Custom Domain** , entrez le nom de votre domaine.
  
    b. Sélectionnez **Enabled** dans **Single Sign-On Status**.

    c. Dans la zone de texte **ID d’entité**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **URL de la page de connexion**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Logout page URL** (URL de la page de déconnexion), collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans **Certificat du fournisseur d’identité**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure. 

    g. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-syncplicity-test-user"></a>Création d’un utilisateur de test Syncplicity
Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Syncplicity. Cette section décrit comment créer des comptes d’utilisateur AAD dans Syncplicity.

**Pour approvisionner un compte d’utilisateur dans Syncplicity, procédez comme suit :**

1. Connectez-vous à votre locataire **Syncplicity** (par exemple : `https://company.Syncplicity.com`).

1. Cliquez sur **admin** et sélectionnez **Comptes d’utilisateur**.

1. Cliquez sur **AJOUTER UN UTILISATEUR**.
   
    ![Gestion des utilisateurs](./media/syncplicity-tutorial/ic769764.png "Gestion des utilisateurs")

1. Type de la **adresses de messagerie** d’un compte AAD que vous souhaitez approvisionner, sélectionnez **utilisateur** comme **rôle**, puis cliquez sur **suivant**.
   
    ![Informations du compte](./media/syncplicity-tutorial/ic769765.png "Informations du compte")
   
    >[!NOTE]
    >Le détenteur du compte AAD reçoit un message électronique contenant un lien pour confirmer et activer le compte. 
    > 

1. Sélectionnez un groupe dans votre société dont votre nouvel utilisateur doit devenir membre, puis cliquez sur **SUIVANT**.
   
    ![Appartenance de groupe](./media/syncplicity-tutorial/ic769772.png "Appartenance de groupe")
   
    >[!NOTE]
    >Si aucun groupe n’est répertorié, cliquez sur **SUIVANT**. 
    > 

1. Sélectionnez les dossiers que vous souhaitez placer sous le contrôle de Syncplicity sur l’ordinateur de l’utilisateur, puis cliquez sur **SUIVANT**.
   
    ![Dossiers Syncplicity](./media/syncplicity-tutorial/ic769773.png "Dossiers Syncplicity")

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Syncplicity, pour approvisionner des comptes utilisateur AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Syncplicity.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Syncplicity, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Syncplicity**.

    ![Configurer l'authentification unique](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Syncplicity dans le panneau d’accès, vous devez être connecté automatiquement à votre application Syncplicity.
## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

