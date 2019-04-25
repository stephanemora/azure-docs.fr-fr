---
title: 'Didacticiel : Intégration d’Azure Active Directory à EverBridge | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1bb62c9a11971f72a6c96c4652b136c19812cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279506"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Didacticiel : Intégration d’Azure Active Directory à Everbridge

Dans ce didacticiel, vous allez apprendre à intégrer EverBridge à Azure Active Directory (Azure AD).

L’intégration d’EverBridge à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à EverBridge.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à EverBridge (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à EverBridge, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement EverBridge pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’EverBridge à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-everbridge-from-the-gallery"></a>Ajout d’EverBridge à partir de la galerie

Pour configurer l’intégration d’EverBridge à Azure AD, vous devez ajouter EverBridge à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter EverBridge à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **EverBridge**, sélectionnez **EverBridge** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![EverBridge dans la liste des résultats](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec EverBridge à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur EverBridge équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur EverBridge associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec EverBridge, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test EverBridge](#creating-an-everbridge-test-user)** pour avoir un équivalent de Britta Simon dans EverBridge lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application EverBridge.

**Pour configurer l’authentification unique Azure AD avec EverBridge, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **EverBridge**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

    >[!NOTE]
    >Vous devez effectuer les configurations de l’application SOIT en tant que portail Manager, SOIT en tant que portail de membre sur les deux extrémités, par exemple, sur le portail Azure et sur le portail d’Everbridge.

4. Pour configurer l’application **EverBridge** en tant que **portail Manager EverBridge**, dans la section **Configuration SAML de base**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://sso.everbridge.net/<API_Name>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez [l’équipe de support technique EverBridge](mailto:support@everbridge.com) pour obtenir ces valeurs.

5. Pour configurer l’application **EverBridge** en tant que **portail de membre EverBridge**, dans la section **Configuration SAML de base**, procédez comme suit :

   * Si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

       ![Informations d’authentification unique dans Domaine et URL EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

       * Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

       * Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

       ![Informations d’authentification unique dans Domaine et URL EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

       * Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL d’authentification et l’URL de réponse réels. Contactez [l’équipe de support technique EverBridge](mailto:support@everbridge.com) pour obtenir ces valeurs.

6. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Dans la section **Configurer EverBridge**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration d’EverBridge](common/configuresection.png)

8. Pour configurer l’authentification unique pour **EverBridge** en tant qu’application **Portail Manager EverBridge**, procédez comme suit : 
 
9. Dans une autre fenêtre de navigateur web, connectez-vous à EverBridge en tant qu’administrateur.

9. Dans le menu situé en haut, cliquez sur l’onglet **Paramètres** et sélectionnez **Authentification unique** sous **Sécurité**.
   
     ![Configurer l'authentification unique](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Dans la zone de texte **Nom**, entrez le nom du fournisseur d’identificateurs (par exemple : le nom de votre entreprise).
   
     b. Dans la zone de texte **nom de l’API** , saisissez le nom de l’API.
   
     c. Cliquez sur le bouton **Choisir un fichier** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.
   
     d. Dans SAML Identity Location (Emplacement de l’identité SAML), sélectionnez **Identity is in the NameIdentifier element of the Subject statement** (L’identité figure dans l’élément NameIdentifier de l’instruction Subject).
   
     e. Dans la zone de texte **URL de connexion du fournisseur d'identité**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.
   
     f. Dans Liaison de demande initiée par le fournisseur de service, sélectionnez **Redirection HTTP**.

     g. Cliquez sur **Enregistrer**.

10. Pour configurer l’authentification unique sur l’application **EverBridge** en tant que **membre de portail EverBridge**, vous devez envoyer le fichier **XML des métadonnées de fédération** à [ L’équipe de support Everbridge](mailto:support@everbridge.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotresociété.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
  
### <a name="creating-an-everbridge-test-user"></a>Création d’un utilisateur de test EverBridge

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Everbridge. Travaillez avec [l’équipe de support technique EverBridge](mailto:support@everbridge.com) pour ajouter les utilisateurs à la plateforme EverBridge.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EverBridge.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **EverBridge**.

    ![Configurer l'authentification unique](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette EverBridge dans le volet d’accès, vous devez être connecté automatiquement à votre application EverBridge.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
