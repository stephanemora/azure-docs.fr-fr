---
title: 'Didacticiel : Intégration d’Azure Active Directory à Slack | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: jeedes
ms.openlocfilehash: 5b1099e46cf1aa2fd4b948fee8407cfd859390ce
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129112"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure AD avec Slack

Dans ce didacticiel, vous allez apprendre à intégrer Slack à Azure Active Directory (Azure AD).

L’intégration de Slack dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Slack
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Slack (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Slack, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Slack pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Slack à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-slack-from-the-gallery"></a>Ajout de Slack à partir de la galerie
Pour configurer l’intégration de Slack avec Azure AD, vous devez ajouter Slack à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Slack à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Slack**.

    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/tutorial_slack_search.png)

1. Dans le volet de résultats, sélectionnez **Slack**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Slack avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Slack équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Slack associé doit être établie.

Dans Slack, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Slack, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Slack](#creating-a-slack-test-user)** pour avoir un équivalent de Britta Simon dans Slack, lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Slack.

**Pour configurer l’authentification unique Azure AD avec Slack, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Slack**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Dans la section **Domaine et URL Slack**, procédez comme suit :

    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.slack.com`

    b. Dans la zone de texte **Identificateur**, remplacez la valeur par l’URL de connexion. Il s’agit du domaine de votre espace de travail. Par exemple : `https://contoso.slack.com`

1. L’application Slack attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Si vous avez des utilisateurs pour lesquels **l’adresse de messagerie** assignée n’est pas une licence Office 365, la revendication **User.Email** n’apparaît pas dans le jeton SAML. Dans ce cas, nous suggérons d’utiliser **user.userprincipalname** comme valeur d’attribut **User.Email** à mapper en tant que **Identificateur Unique** à la place.

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, sélectionnez **user.mail** en tant **qu’identificateur d’utilisateur**, et pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |  
    | User.Username | user.userprincipalname |

    a. Cliquez sur **Attribut** pour ouvrir la boîte de dialogue **Modifier l’attribut** et effectuez les opérations suivantes :

    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    b. Dans la liste **Valeur** , sélectionnez la valeur de l’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Cliquez sur **OK**

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Slack** , cliquez sur **Configurer Slack** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Slack en tant qu’administrateur.

1. Accédez à **Microsoft Azure AD**, puis à **Team Settings**.

     ![Configurer l’authentification unique côté application](./media/slack-tutorial/tutorial_slack_001.png)

1. Dans la section **Team Settings**, cliquez sur l’onglet **Authentication** puis sur **Change Settings**.

    ![Configurer l’authentification unique côté application](./media/slack-tutorial/tutorial_slack_002.png)

1. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Dans la zone de texte **Point de terminaison SAML 2.0 (HTTP)**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    b.  Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**ID d’entité SAML** copié à partir du portail Azure.

    c.  Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

    d. Configurez les trois paramètres ci-dessus comme nécessaire pour votre équipe Slack. Pour plus d’informations sur les paramètres, vous trouverez le **guide de configuration de l’authentification unique sur Slack**. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Cliquez sur **Enregistrer la configuration**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.

    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/slack-tutorial/create_aaduser_04.png)

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="creating-a-slack-test-user"></a>Création d’un utilisateur de test Slack

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Slack. Slack prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Slack s’il n’existe pas déjà. Slack prend également en charge l’attribution automatique d’utilisateurs, vous trouverez plus d’informations [ici](slack-provisioning-tutorial.md) sur la façon de configurer l’attribution automatique d’utilisateurs.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support technique Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect est l’outil de synchronisation qui peut synchroniser les identités Active Directory locales avec Azure AD. Ces utilisateurs synchronisés peuvent également utiliser les applications comme d’autres utilisateurs du cloud.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Slack.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Slack, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Slack**.

    ![Configurer l'authentification unique](./media/slack-tutorial/tutorial_slack_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Slack dans le volet d’accès, vous devez être connecté automatiquement à votre application Slack.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
