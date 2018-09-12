---
title: 'Didacticiel : Intégration d’Azure Active Directory à XMatters OnDemand | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: a235b85887e64e0a5ca35aae8f31734250a78bb5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160232"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Didacticiel : Intégration d’Azure Active Directory à xMatters OnDemand

Dans ce didacticiel, vous allez découvrir comment intégrer xMatters OnDemand à Azure Active Directory (Azure AD).

L’intégration de xMatters OnDemand à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à xMatters OnDemand
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à xMatters OnDemand (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à xMatters OnDemand, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement xMatters OnDemand pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de xMatters OnDemand à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Ajout de xMatters OnDemand à partir de la galerie
Pour configurer l’intégration de xMatters OnDemand à Azure AD, vous devez ajouter xMatters OnDemand disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter xMatters OnDemand à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **xMatters OnDemand**.

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. Dans le volet de résultats, sélectionnez **xMatters OnDemand**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec xMatters OnDemand avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur xMatters OnDemand équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur xMatters OnDemand associé doit être établie.

Dans xMatters OnDemand, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec xMatters OnDemand, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test xMatters OnDemand](#creating-a-xmatters-ondemand-test-user)** pour avoir un équivalent de Britta Simon dans xMatters OnDemand lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application xMatters OnDemand.

**Pour configurer l’authentification unique Azure AD avec xMatters OnDemand, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **xMatters OnDemand**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. Dans la section **Domaine et URL xMatters OnDemand**, effectuez les étapes suivantes :

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[l’équipe de support technique de xMatters OnDemand](https://www.xmatters.com/company/contact-us/).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (Base64)**, puis enregistrez le fichier du certificat localement en tant que **c:\\XMatters OnDemand.cer**.

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Vous devez transférer le certificat à l’[équipe de support technique de xMatters OnDemand](https://www.xmatters.com/company/contact-us/). L’équipe du support technique xMatters doit charger le certificat avant que vous ne puissiez finaliser la configuration de l’authentification unique. 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de xMatters OnDemand**, cliquez sur **Configurer xMatters OnDemand** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise XMatters OnDemand en tant qu’administrateur.

1. Dans la barre d’outils de la partie supérieure, cliquez sur **Admin**, puis sur **Company Details** dans la barre de navigation située à gauche.

    ![Administrateur](./media/xmatters-ondemand-tutorial/IC776795.png "Administrateur")

1. Dans la page **SAML Configuration** , procédez comme suit :

    ![Configuration SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Configuration SAML")

    a. Sélectionnez **Enable SAML**.

    b. Dans la zone de texte **ID de fournisseur d’identité**, collez la valeur de **l’ID d’entité SAML** copiée sur le portail Azure.

    c. Dans la zone de texte **URL d’authentification unique**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    d. Dans la zone de texte **URL Single Logout**, collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la partie supérieure de la page Company Details, cliquez sur **Save Changes**.

    ![Détails sur la société](./media/xmatters-ondemand-tutorial/IC776797.png "Détails sur la société")

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Création d’un utilisateur de test xMatters OnDemand

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans xMatters OnDemand.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre client **xMatters OnDemand** .

1.  Cliquez sur l’onglet **Utilisateurs**, puis sur **Ajouter un utilisateur**.

    ![Utilisateurs](./media/xmatters-ondemand-tutorial/IC781048.png "Utilisateurs")

1. Dans la section **Add a User** , procédez comme suit :

    ![Ajouter un utilisateur](./media/xmatters-ondemand-tutorial/IC781049.png "Ajouter un utilisateur")

    a. Sélectionnez **Active**.

    b. Dans la zone de texte **User ID**, entrez l’identifiant d’un utilisateur, par exemple Brittasimon@contoso.com.

    c. Dans la zone de texte **First Name**, entrez le prénom de l’utilisateur, par exemple Britta.

    d. Dans la zone de texte **Last Name**, entrez le nom de l’utilisateur, par exemple Simon.

    e. Dans la zone de texte **Site**, entrez le site valide d’un compte Azure AD valide que vous voulez approvisionner.

    f. Cliquez sur **Enregistrer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à xMatters OnDemand.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à xMatters OnDemand, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **xMatters OnDemand**.

    ![Configure Single Sign-On](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette xMatters OnDemand dans le volet d’accès, vous devez être connecté automatiquement à votre application xMatters OnDemand.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
