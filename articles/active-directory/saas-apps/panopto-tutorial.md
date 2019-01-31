---
title: 'Didacticiel : Intégration d’Azure Active Directory à Panopto | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 703b8a7c5a39ee38d5f365474f9338d490b7b980
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179529"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutoriel : Intégration d’Azure Active Directory à Panopto

Dans ce didacticiel, vous allez apprendre à intégrer Panopto à Azure Active Directory (Azure AD).

L’intégration de Panopto à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Panopto.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Panopto (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Panopto, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Panopto pour lequel l’authentification unique est activée.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Panopto à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-panopto-from-the-gallery"></a>Ajout de Panopto à partir de la galerie
Pour configurer l’intégration de Panopto à Azure AD, vous devez ajouter Panopto à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Panopto à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Panopto**.

    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/tutorial_panopto_search.png)

1. Dans le panneau des résultats, sélectionnez **Panopto**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Panopto avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Panopto équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Panopto associé doit être établie.

Dans Panopto, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Panopto, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Panopto](#creating-a-panopto-test-user)** pour obtenir un équivalent de Britta Simon dans Panopto lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Panopto.

**Pour configurer l’authentification unique Azure AD avec Panopto, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Panopto**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/panopto-tutorial/tutorial_panopto_samlbase.png)

1. Dans la section **Domaine et URL Panopto**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/panopto-tutorial/tutorial_panopto_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenant-name>.panopto.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez [l’équipe de support technique Panopto](mailto:support@panopto.com‎) pour obtenir cette valeur. 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/panopto-tutorial/tutorial_panopto_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/panopto-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Panopto**, cliquez sur **Configurer Panopto** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l'authentification unique](./media/panopto-tutorial/tutorial_panopto_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Panopto en tant qu’administrateur.

1. Dans la barre d’outils située sur la gauche, cliquez sur **System (Système)**, puis sur **Identity Providers (Fournisseurs d’identité)**.
   
   ![Système](./media/panopto-tutorial/ic777670.png "système")
1. Cliquez sur **Add Provider**.
   
   ![Fournisseurs d’identité](./media/panopto-tutorial/ic777671.png "fournisseurs d’identité")
   
1. Dans la section du fournisseur SAML, procédez comme suit :
   
    ![Configuration SaaS](./media/panopto-tutorial/ic777672.png "configuration SaaS")
    
    a. Dans la liste **Provider Type (Type de fournisseur)**, sélectionnez **SAML20**.    
    
    b. Dans la zone de texte **Instance Name** , attribuez un nom à votre instance.

    c. Dans la zone de texte **Friendly Description** , entrez une description conviviale.
    
    d. Dans la zone de texte **URL de la page de rebond**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    e. Dans la zone de texte **Émetteur**, collez la valeur de **ID d’entité SAML** que vous avez copiée depuis le portail Azure.

    f. Ouvrez votre certificat codé en base 64, téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Clé publique**.

1. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/panopto-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-panopto-test-user"></a>Création d’un utilisateur de test Panopto

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Panopto.  
Lorsqu’un utilisateur affecté tente de se connecter à Panopto à l’aide du panneau d’accès, Panopto vérifie si cet utilisateur existe.  

Si aucun compte d’utilisateur n’est disponible, Panopto le crée automatiquement.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Panopto, pour approvisionner des comptes utilisateur AAD.
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Panopto.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Panopto, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Panopto**.

    ![Configurer l'authentification unique](./media/panopto-tutorial/tutorial_panopto_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Panopto dans le panneau d’accès, vous accédez automatiquement à la page de connexion Panopto.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/panopto-tutorial/tutorial_general_01.png
[2]: ./media/panopto-tutorial/tutorial_general_02.png
[3]: ./media/panopto-tutorial/tutorial_general_03.png
[4]: ./media/panopto-tutorial/tutorial_general_04.png

[100]: ./media/panopto-tutorial/tutorial_general_100.png

[200]: ./media/panopto-tutorial/tutorial_general_200.png
[201]: ./media/panopto-tutorial/tutorial_general_201.png
[202]: ./media/panopto-tutorial/tutorial_general_202.png
[203]: ./media/panopto-tutorial/tutorial_general_203.png

