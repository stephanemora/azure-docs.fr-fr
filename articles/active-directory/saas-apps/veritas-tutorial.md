---
title: 'Didacticiel : intégration d’Azure Active Directory à Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Veritas Enterprise Vault.cloud SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: 4ff282b3db4689ceaf5fa27b57c82cb05025712e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449095"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Didacticiel : intégration d’Azure Active Directory à Veritas Enterprise Vault.cloud SSO

Dans ce didacticiel, vous allez apprendre à intégrer Veritas Enterprise Vault.cloud SSO à Azure Active Directory (Azure AD).

L’intégration du logiciel Veritas Enterprise Vault.cloud SSO à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Veritas Enterprise Vault.cloud SSO
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Veritas Enterprise Vault.cloud SSO (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Veritas Enterprise Vault.cloud SSO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Veritas Enterprise Vault.cloud SSO pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Veritas Enterprise SSO Vault.cloud à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Ajout de Veritas Enterprise SSO Vault.cloud à partir de la galerie
Pour configurer son intégration à Azure AD, vous devez ajouter Veritas Enterprise Vault.cloud SSO à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Veritas Enterprise Vault.cloud SSO à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Veritas Enterprise Vault.cloud SSO**.

    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/tutorial_veritas_search.png)

1. Dans le volet des résultats, sélectionnez **Veritas Enterprise Vault.cloud SSO**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Veritas Enterprise Vault.cloud SSO équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et un utilisateur Veritas Enterprise Vault.cloud SSO associé.

Dans Veritas Enterprise Vault.cloud SSO, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Veritas Enterprise Vault.cloud SSO](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)** pour avoir un équivalent de Britta Simon dans Veritas Enterprise Vault.cloud SSO, lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Veritas Enterprise Vault.cloud SSO.

**Pour configurer l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Veritas Enterprise Vault.cloud SSO**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/veritas-tutorial/tutorial_veritas_samlbase.png)

1. Dans la section **Domaine et URL Veritas Enterprise Vault.cloud SSO**, procédez comme suit :

    ![Configurer l'authentification unique](./media/veritas-tutorial/tutorial_veritas_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Dans la zone de texte **Identificateur**, utilisez l’URL du Centre de données.

    | Centre de données| URL |
    |----------|----|
    | Amérique du Nord| `https://auth.lax.archivecloud.net` |
    | Europe | `https://auth.ams.archivecloud.net` |
    | Asie-Pacifique| `https://auth.syd.archivecloud.net`|

    c. Dans la zone de texte **URL de réponse**, utilisez l’URL du Centre de données.

    | Centre de données| URL |
    |----------|----|
    | Amérique du Nord| `https://auth.lax.archivecloud.net` |
    | Europe | `https://auth.ams.archivecloud.net` |
    | Asie-Pacifique| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez l’[équipe de support Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) pour obtenir cette valeur. 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/veritas-tutorial/tutorial_veritas_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/veritas-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Veritas Enterprise Vault.cloud SSO**, cliquez sur **Configurer Veritas Enterprise Vault.cloud SSO** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/veritas-tutorial/tutorial_veritas_configure.png) 

1. Pour configurer l’authentification unique côté **Veritas Enterprise Vault.cloud SSO**, vous devez envoyer le **Certificat (Base64)** téléchargé et **l’URL du service d’authentification unique SAML** à [l’équipe de support technique Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/veritas-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Création d’un utilisateur de test Veritas Enterprise Vault.cloud SSO

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Enterprise Vault.cloud SSO. Travaillez avec l’[équipe de support technique Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) pour ajouter les utilisateurs dans la plateforme Enterprise Vault.cloud SSO. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Veritas Enterprise Vault.cloud SSO.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Veritas Enterprise Vault.cloud SSO, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Veritas Enterprise Vault.cloud SSO**.

    ![Configurer l'authentification unique](./media/veritas-tutorial/tutorial_veritas_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Veritas Enterprise Vault.cloud SSO dans le volet d’accès, vous devez être connecté automatiquement à votre application Veritas Enterprise Vault.cloud SSO.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/veritas-tutorial/tutorial_general_01.png
[2]: ./media/veritas-tutorial/tutorial_general_02.png
[3]: ./media/veritas-tutorial/tutorial_general_03.png
[4]: ./media/veritas-tutorial/tutorial_general_04.png

[100]: ./media/veritas-tutorial/tutorial_general_100.png

[200]: ./media/veritas-tutorial/tutorial_general_200.png
[201]: ./media/veritas-tutorial/tutorial_general_201.png
[202]: ./media/veritas-tutorial/tutorial_general_202.png
[203]: ./media/veritas-tutorial/tutorial_general_203.png

