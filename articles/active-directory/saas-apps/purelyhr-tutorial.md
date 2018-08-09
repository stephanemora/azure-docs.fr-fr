---
title: 'Didacticiel : Intégration d’Azure Active Directory avec PurelyHR | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: c02dc274c5d22c16b2bda6d7896ee64c41d6e2ab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438992"
---
# <a name="tutorial-azure-active-directory-integration-with-purelyhr"></a>Didacticiel : Intégration d’Azure Active Directory avec PurelyHR

Dans ce didacticiel, vous allez apprendre à intégrer PurelyHR avec Azure Active Directory (Azure AD).

L’intégration de PurelyHR avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à PurelyHR
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à PurelyHR (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec PurelyHR, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement PurelyHR pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de PurelyHR à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-purelyhr-from-the-gallery"></a>Ajout de PurelyHR à partir de la galerie
Pour configurer l’intégration de PurelyHR avec Azure AD, vous devez ajouter PurelyHR à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PurelyHR à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans le champ de recherche, tapez **PurelyHR**.

    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_search.png)

1. Dans le volet de résultats, sélectionnez **PurelyHR**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec PurelyHR, par le biais d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur PurelyHR correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur PurelyHR associé doit être établie.

Dans PurelyHR, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec PurelyHR, vous devez vous conformer aux indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test PurelyHR](#creating-a-purelyhr-test-user)** pour avoir un équivalent de Britta Simon dans PurelyHR lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application PurelyHR.

**Pour configurer l’authentification unique Azure AD avec PurelyHR, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **PurelyHR**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_purelyhr_samlbase.png)

1. Dans la section **Domaines et URL PurelyHR**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_purelyhr_url.png)
   
    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<companyID>.purelyhr.com/sso-consume`

1. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_purelyhr_url1.png)
    
    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<companyID>.purelyhr.com/sso-initiate`
     
    > [!NOTE]
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique PurelyHR](http://support.purelyhr.com/). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/purelyhr-tutorial/tutorial_purelyhr_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration de PurelyHR**, cliquez sur **Configurer PurelyHR** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_purelyhr_configure.png) 

1. Pour configurer l’authentification unique côté **PurelyHR**, connectez-vous au site web en tant qu’administrateur.

1. Ouvrez le **tableau de bord** dans la barre d’outils et cliquez sur **SSO Settings** (Paramètres d’authentification unique).

1. Collez les valeurs dans les zones comme décrit ci-dessous :

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Ouvrez le **Certificat (Base64)** téléchargé à partir du portail Azure dans le bloc-notes et copiez la valeur du certificat. Collez la valeur copiée dans la zone **Certificat x509**.

    b. Dans la zone **Idp Issuer URL** (URL d’émetteur IdP), collez l’**ID d’entité SAML** copiée à partir du portail Azure.

    c. Dans la zone **Idp Endpoint URL** (URL de point de terminaison IdP), collez l’**URL d’authentification unique SAML** copiée à partir du portail Azure. 

    d. Cochez la case **Auto-Create Users** (Créer automatiquement des utilisateurs) pour activer l’approvisionnement automatique des utilisateurs dans PurelyHR.

    e. Cliquez sur **Enregistrer les modifications** pour enregistrer les paramètres.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/purelyhr-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-purelyhr-test-user"></a>Création d’un utilisateur de test PurelyHR

Pour se connecter à PurelyHR, les utilisateurs d’Azure AD doivent être approvisionnés dans PurelyHR. Dans PurelyHR, l’approvisionnement est une tâche automatique. Aucune opération manuelle n’est nécessaire lorsque l’approvisionnement automatique de l’utilisateur est activé.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PurelyHR.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à PurelyHR, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **PurelyHR**.

    ![Configurer l'authentification unique](./media/purelyhr-tutorial/tutorial_purelyhr_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Absorb LMS dans le volet d’accès, vous êtes automatiquement connecté à votre application Absorb LMS.

Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/purelyhr-tutorial/tutorial_general_01.png
[2]: ./media/purelyhr-tutorial/tutorial_general_02.png
[3]: ./media/purelyhr-tutorial/tutorial_general_03.png
[4]: ./media/purelyhr-tutorial/tutorial_general_04.png

[100]: ./media/purelyhr-tutorial/tutorial_general_100.png

[200]: ./media/purelyhr-tutorial/tutorial_general_200.png
[201]: ./media/purelyhr-tutorial/tutorial_general_201.png
[202]: ./media/purelyhr-tutorial/tutorial_general_202.png
[203]: ./media/purelyhr-tutorial/tutorial_general_203.png

