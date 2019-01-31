---
title: 'Tutoriel : Intégration d’Azure Active Directory à HPE SaaS | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HPE SaaS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 314003d6-ca66-4456-88c3-934254d4a9a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: jeedes
ms.openlocfilehash: 5d87a78d2fd918fde34c861f1066139bed1a1fef
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197787"
---
# <a name="tutorial-azure-active-directory-integration-with-hpe-saas"></a>Didacticiel : Intégration d’Azure Active Directory à HPE SaaS

Dans ce didacticiel, vous allez apprendre à intégrer HPE SaaS dans Azure Active Directory (Azure AD).

L’intégration de HPE SaaS dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HPE SaaS.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à HPE SaaS (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à HPE SaaS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement HPE SaaS pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [Offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HPE SaaS à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hpe-saas-from-the-gallery"></a>Ajout de HPE SaaS à partir de la galerie
Pour configurer l’intégration de HPE SaaS à Azure AD, vous devez ajouter HPE SaaS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HPE SaaS à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **HPE SaaS**.

    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/tutorial_hpesaas_search.png)

1. Dans le panneau des résultats, sélectionnez **HPE SaaS**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/tutorial_hpesaas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec HPE SaaS avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HPE SaaS équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur HPE SaaS associé doit être établie.

Dans HPE SaaS, assignez la valeur du **nom d’utilisateur** d’Azure AD comme valeur de **Username** (Nom d’utilisateur) pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec HPE SaaS, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test HPE SaaS](#creating-an-hpe-saas-test-user)** pour avoir dans HPE SaaS un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application HPE SaaS.

**Pour configurer l’authentification unique Azure AD avec HPE SaaS, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **HPE SaaS**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/hpesaas-tutorial/tutorial_hpesaas_samlbase.png)

1. Dans la section **HPE SaaS Domain and URLs** (Domaine et URL HPE SaaS), procédez comme suit :

    ![Configurer l'authentification unique](./media/hpesaas-tutorial/tutorial_hpesaas_url.png)

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://login.saas.hpe.com/msg`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.saas.hpe.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support client HPE SaaS](https://saas.hpe.com/en-us/contact). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/hpesaas-tutorial/tutorial_hpesaas_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/hpesaas-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique du côté **HPE SaaS**, vous devez envoyer le **XML des métadonnées** téléchargé à [l’équipe de support HPE SaaS](https://saas.hpe.com/en-us/contact). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/hpesaas-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-hpe-saas-test-user"></a>Création d’un utilisateur de test HPE SaaS

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HPE SaaS. Pour ajouter des utilisateurs au compte HPE SaaS, contactez l’[équipe de support HPE SaaS](https://saas.hpe.com/en-us/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HPE SaaS.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à HPE SaaS, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **HPE SaaS**.

    ![Configurer l'authentification unique](./media/hpesaas-tutorial/tutorial_hpesaas_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette HPE SaaS dans le volet d’accès, vous devez être connecté automatiquement à votre application HPE SaaS.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hpesaas-tutorial/tutorial_general_01.png
[2]: ./media/hpesaas-tutorial/tutorial_general_02.png
[3]: ./media/hpesaas-tutorial/tutorial_general_03.png
[4]: ./media/hpesaas-tutorial/tutorial_general_04.png

[100]: ./media/hpesaas-tutorial/tutorial_general_100.png

[200]: ./media/hpesaas-tutorial/tutorial_general_200.png
[201]: ./media/hpesaas-tutorial/tutorial_general_201.png
[202]: ./media/hpesaas-tutorial/tutorial_general_202.png
[203]: ./media/hpesaas-tutorial/tutorial_general_203.png

