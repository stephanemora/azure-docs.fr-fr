---
title: 'Tutoriel : Intégration d’Azure Active Directory à Elium | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 2c41b2c297d8d89554950e8c77201ff5bedf743e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Tutoriel : Intégration d’Azure Active Directory à Elium

Dans ce tutoriel, vous allez découvrir comment intégrer Elium à Azure Active Directory (Azure AD).

L’intégration d’Elium dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Elium.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Elium (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Elium, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Elium pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Elium depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-elium-from-the-gallery"></a>Ajout d’Elium depuis la galerie
Pour configurer l’intégration d’Elium à Azure AD, vous devez ajouter Elium, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Elium à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Elium**, sélectionnez **Elium** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Elium dans la liste des résultats](./media/active-directory-saas-elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Elium sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Elium équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Elium associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Elium, vous devez suivre les indications des modules suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Elium](#create-an-elium-test-user)** pour avoir dans Elium un équivalent de Britta Simon associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Elium.

**Pour configurer l’authentification unique Azure AD avec Elium, suivez les étapes ci-dessous :**

1. Dans la page d’intégration de l’application **Elium** du portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-elium-tutorial/tutorial_elium_samlbase.png)

3. Dans la section **Domaines et URL Elium**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Elium](./media/active-directory-saas-elium-tutorial/tutorial_elium_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<platform-domain>.elium.com/login/saml2/acs`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Elium](./media/active-directory-saas-elium-tutorial/tutorial_elium_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous obtenez ces valeurs à partir du **fichier de métadonnées SP** téléchargeable à l’adresse `https://<platform-domain>.elium.com/login/saml2/metadata`, procédure qui est expliquée plus loin dans ce tutoriel.

5. L’application Elium attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :
           
    | Nom de l'attribut | Valeur de l’attribut |   
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | fonction| user.jobtitle|
    | société| user.companyname|
    
    > [!NOTE]
    > Ce sont les revendications par défaut. **Seule la revendication d’e-mail est obligatoire**. Pour le provisionnement JIT, cette revendication est également la seule obligatoire. D’autres revendications personnalisées peuvent varier d’une plateforme de client à l’autre.

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute_04.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute_05.png)

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez l’espace de noms vide.
    
    e. Cliquez sur **OK**. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-elium-tutorial/tutorial_elium_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-elium-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Elium en tant qu’administrateur.

8. Cliquez sur le **Profil utilisateur** dans le coin supérieur droit, puis sélectionnez **Administration**.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user1.png)

9. Sélectionnez l’onglet **Sécurité**.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user2.png)

10. Faites défiler jusqu’à la section **Single Sign-On SSO** (Authentification unique) et effectuez les étapes suivantes :

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user3.png)

    a. Copiez la valeur de **Verify that SAML2 authentication works for your account** (Vérifiez que l’authentification SAML2 fonctionne pour votre compte) et collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL Elium** du portail Azure.

    > [!NOTE]
    > Après avoir configuré l’authentification unique, vous conservez l’accès à la page de connexion à distance par défaut en utilisant l’URL suivante : `https://<platform_domain>/login/regular/login` 

    b. Sélectionnez la case à cocher **Enable SAML2 federation** (Activer la fédération SAML2).

    c. Sélectionnez la case à cocher **JIT Provisioning** (Provisionnement JIT).

    d. Ouvrez **SP Metadata** (Métadonnées SP) en cliquant sur le bouton **Download** (Télécharger).

    e. Recherchez l’**entityID** (identificateur d’utilisateur) dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **entityID** et collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL Elium** du portail Azure. 

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user4.png)

    f. Recherchez **AssertionConsumerService** dans le fichier **SP Metadata** (Métadonnées du fournisseur de service), copiez la valeur de **Location** et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Elium** du portail Azure.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user5.png)

    g. Dans le Bloc-notes, ouvrez le fichier de métadonnées téléchargé depuis le portail Azure, copiez son contenu et collez-le dans la zone de texte **IdP Metadata** (Métadonnées du fournisseur d’identité).

    h. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-elium-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-elium-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-elium-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-elium-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-elium-test-user"></a>Créer un utilisateur de test Elium

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Elium. Elium prend en charge le provisionnement juste-à-temps, une option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Elium.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant un accès à Elium.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Elium, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Elium**.

    ![Lien Elium dans la liste des applications](./media/active-directory-saas-elium-tutorial/tutorial_elium_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Elium du panneau d’accès, vous devriez être connecté automatiquement à votre application Elium.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-elium-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-elium-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-elium-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-elium-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-elium-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-elium-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-elium-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-elium-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-elium-tutorial/tutorial_general_203.png

