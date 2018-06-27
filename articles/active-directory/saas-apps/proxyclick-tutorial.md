---
title: 'Didacticiel : Intégration d’Azure Active Directory à Proxyclick | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: 6e0d8c5c178afc63c9c177d31d0a14104b127941
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213336"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Didacticiel : Intégration d’Azure Active Directory à Proxyclick

Ce didacticiel vous montrera comment intégrer Proxyclick à Azure Active Directory (Azure AD).

L’intégration de Proxyclick dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Proxyclick.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Proxyclick (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Proxyclick, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Proxyclick pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Proxyclick à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-proxyclick-from-the-gallery"></a>Ajout de Proxyclick à partir de la galerie
Pour configurer l’intégration de Proxyclick à Azure AD, vous devez ajouter Proxyclick, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Proxyclick à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Proxyclick**, sélectionnez **Proxyclick** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Proxyclick dans la liste des résultats](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Proxyclick sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Proxyclick équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Proxyclick associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Proxyclick, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Proxyclick](#create-a-proxyclick-test-user)** pour avoir un équivalent de Britta Simon dans Proxyclick lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Proxyclick.

**Pour configurer l’authentification unique Azure AD avec Proxyclick, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Proxyclick**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

3. Dans la section **Domaines et URL Proxyclick**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://saml.proxyclick.com/init/<companyId>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://saml.proxyclick.com/consume/<companyId>`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous mettrez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/proxyclick-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de Proxyclick**, cliquez sur **Configurer Proxyclick** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

8. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Proxyclick en tant qu’administrateur.

9. Sélectionnez **Compte et paramètres**.

    ![Configuration de Proxyclick](./media/proxyclick-tutorial/configure1.png)

10. Faites défiler jusqu'à **INTÉGRATIONS** et sélectionnez **SAML**.

    ![Configuration de Proxyclick](./media/proxyclick-tutorial/configure2.png)

11. Dans la section **SAML**, procédez comme suit :

    ![Configuration de Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Copiez la valeur **URL de consommateur SAML** et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Proxyclick** du portail Azure.

    b. Copiez la valeur **URL de redirection d’authentification unique SAML** et collez-la dans les zones de texte **URL de connexion** et **Identificateur** de la section **Domaine et URL Proxyclick** du portail Azure.

    c. Sélectionnez **Méthode de requête SAML** pour **Redirection HTTP**.

    d. Dans la zone de texte **Émetteur**, collez la valeur de **ID d’entité SAML** que vous avez copiée depuis le portail Azure.

    e. Dans la zone de texte **URL du point de terminaison SAML 2.0**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    f. Dans le Bloc-notes, ouvrez le fichier de certificat que vous avez téléchargé sur le portail Azure, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.

    g. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/proxyclick-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/proxyclick-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-proxyclick-test-user"></a>Créer un utilisateur de test Proxyclick

Pour permettre aux utilisateurs Azure AD de se connecter à Proxyclick, vous devez les approvisionner dans Proxyclick. Dans le cas de Proxyclick, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Proxyclick en tant qu’administrateur.

2. Cliquez sur **Collègues** dans la barre de navigation supérieure.

    ![Ajouter un employé](./media/proxyclick-tutorial/user1.png)

3. Cliquez sur **Ajouter un collègue**

    ![Ajouter un employé](./media/proxyclick-tutorial/user2.png)

4. Dans la section **Ajouter un collègue**, procédez comme suit :

    ![Ajouter un employé](./media/proxyclick-tutorial/user3.png)

    a. Dans la zone de texte **E-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com**.

    b. Dans la zone de texte **Prénom**, tapez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom de famille**, tapez le nom de l’utilisateur, par exemple Simon.

    d. Cliquez sur **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Proxyclick.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Proxyclick, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Proxyclick**.

    ![Lien Proxyclick dans la liste des applications](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Proxyclick dans le volet d’accès, vous devez être connecté automatiquement à votre application Proxyclick.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

