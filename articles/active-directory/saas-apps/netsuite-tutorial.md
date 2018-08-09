---
title: 'Tutoriel : Intégration d’Azure Active Directory à NetSuite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431404"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutoriel : Intégration d’Azure Active Directory à NetSuite

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à NetSuite.

L’intégration de NetSuite à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à NetSuite.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à NetSuite (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à NetSuite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement NetSuite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de NetSuite à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-netsuite-from-the-gallery"></a>Ajout de NetSuite à partir de la galerie
Pour configurer l’intégration de NetSuite à Azure AD, vous devez ajouter NetSuite à votre liste d’applications SaaS managées, à partir de la galerie.

**Pour ajouter NetSuite à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]

1. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **NetSuite**, sélectionnez **NetSuite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![NetSuite dans la liste des résultats](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec NetSuite sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur NetSuite équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur NetSuite qui lui est associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans NetSuite.

Pour configurer et tester l’authentification unique Azure AD avec NetSuite, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test NetSuite](#creating-a-netsuite-test-user)** : pour avoir un équivalent de Britta Simon dans NetSuite qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application NetSuite.

**Pour configurer l’authentification unique Azure AD avec NetSuite, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **NetSuite**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. Dans la section **Domaine et URL NetSuite**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse réel. Pour obtenir ces valeurs, contactez [l’équipe du support technique NetSuite](http://www.NetSuite.com/portal/services/support.shtml).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de NetSuite**, cliquez sur **Configurer NetSuite** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise NetSuite en tant qu’administrateur.

1. Dans la barre d’outils en haut de la page, cliquez sur **Setup** (Configuration), puis accédez à **Company** (Entreprise) et cliquez sur **Enable Features** (Activer des fonctionnalités).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setupsaml.png)

1. Dans la barre d’outils située au milieu de la page, cliquez sur **SuiteCloud**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-suitecloud.png)

1. Dans la section **Manage Authentication** (Gérer l’authentification), sélectionnez **SAML SINGLE SIGN-ON** (Authentification unique SAML) pour activer l’option du même nom dans NetSuite.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-ticksaml.png)

1. Dans la barre d’outils située en haut de la page, cliquez sur **Setup** (Configuration).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

1. Dans la liste **SETUP TASKS** (Tâches de configuration), sélectionnez **Integration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-integration.png)

1. Dans la section **MANAGE AUTHENTICATION** (Gérer l’authentification), cliquez sur **SAML Single Sign-on** (Authentification unique SAML).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-saml.png)

1. Dans la section **SAML Setup** (Configuration SAML) située sous **NetSuite Configuration** (Configuration NetSuite), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Sélectionnez **PRIMARY AUTHENTICATION METHOD** (Méthode d’authentification principale).

    b. Pour le champ intitulé **SAMLV2 IDENTITY PROVIDER METADATA** (Métadonnées du fournisseur d’identité SAMLV2), sélectionnez **UPLOAD IDP METADATA FILE** (Charger le fichier de métadonnées du fournisseur d’identité). Cliquez ensuite sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Cliquez sur **Envoyer**.

1. Dans Azure AD, activez la case à cocher **Afficher et modifier tous les autres attributs utilisateur**, puis ajoutez un attribut.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-attributes.png)

1. Dans le champ **Nom de l’attribut**, entrez `account`. Dans le champ **Valeur de l’attribut** , entrez votre ID de compte NetSuite. Cette valeur est constante et varie selon le compte. Vous trouverez ci-dessous des instructions sur la recherche de votre ID de compte :

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. Dans NetSuite, cliquez sur **Setup** (Configuration), puis accédez à **Company** (Entreprise) et cliquez sur **Company Information** (Informations sur l’entreprise) dans le menu de navigation supérieur.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Dans la page **Company Information** (Informations sur l’entreprise), dans la colonne de droite, copiez la valeur du champ **ACCOUNT ID** (ID de compte).

    c. Collez **l’ID de compte** du compte NetSuite dans le champ **Valeur d’attribut** d’Azure AD. 

1. Avant que les utilisateurs puissent utiliser l’authentification unique dans NetSuite, vous devez d’abord leur affecter les autorisations appropriées dans NetSuite. Procédez comme suit pour attribuer ces instructions.

    a. Dans le menu de navigation supérieur, cliquez sur **Setup** (Configuration).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

    b. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les rôles**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Cliquez sur **Nouveau rôle**.

    d. Entrez un **nom** pour votre nouveau projet.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-new-role.png)

    e. Cliquez sur **Enregistrer**.

    f. Dans le menu situé en haut, cliquez sur **Autorisations**. Cliquez sur **Configuration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-sso.png)

    g. Sélectionnez **SAML Single Sign-on** (Authentification unique SAML), puis cliquez sur **Add** (Ajouter).

    h. Cliquez sur **Enregistrer**.

    i. Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

    j. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les utilisateurs**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Sélectionnez un utilisateur de test. Ensuite, cliquez sur **Edit** (Modifier), puis accédez à l’onglet **Access** (Accès).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Dans la boîte de dialogue Roles (Rôles), attribuez le rôle que vous avez créé.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-add-role.png)

    m. Cliquez sur **Enregistrer**.
 
### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**. 

### <a name="creating-a-netsuite-test-user"></a>Création d’un utilisateur de test NetSuite

Dans cette section, un utilisateur nommé Britta Simon est créé dans NetSuite. NetSuite prend en charge le provisionnement juste-à-temps, qui est activé par défaut.
Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans NetSuite, un nouveau est créé lorsque vous tentez d’accéder à NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à NetSuite.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à NetSuite, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **NetSuite**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **NetSuite**.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

