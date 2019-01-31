---
title: 'Tutoriel : Intégration d’Azure Active Directory à direct | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et direct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7c2cd1f0-d14c-42f0-94a8-9b800008b285
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: jeedes
ms.openlocfilehash: f45e3c5a3416c28ebe16fcf0ba1f016478c46c7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176229"
---
# <a name="tutorial-azure-active-directory-integration-with-direct"></a>Tutoriel : Intégration d’Azure Active Directory à direct

Dans ce didacticiel, vous allez apprendre à intégrer direct à Azure Active Directory (Azure AD).

L’intégration de direct à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à direct
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à direct (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec direct, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement direct pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de direct à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-direct-from-the-gallery"></a>Ajout de direct à partir de la galerie

Pour configurer l’intégration de direct avec Azure AD, vous devez ajouter direct à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter direct à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **direct**. Sélectionnez **direct** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/direct-tutorial/tutorial_direct_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec direct, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur direct correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur direct associé doit être établie.

Dans direct, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec direct, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test direct](#creating-a-direct-test-user)** pour avoir un équivalent de Britta Simon dans direct lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application direct.

**Pour configurer l’authentification unique Azure AD avec direct, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **direct**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/direct-tutorial/tutorial_direct_samlbase.png)

3. Dans la section **Domaine et URL direct**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Configurer l'authentification unique](./media/direct-tutorial/tutorial_direct_url.png)

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://direct4b.com/`

4. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configure Single Sign-On](./media/direct-tutorial/tutorial_direct_url1.png)

     Dans la zone de texte **URL de connexion**, tapez l’URL : `https://direct4b.com/sso` 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/direct-tutorial/tutorial_direct_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/direct-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté **direct**, vous devez envoyer le **XML de métadonnées** téléchargé à [l’équipe de support technique de direct](https://direct4b.com/ja/support.html#inquiry).

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/direct-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/direct-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.

    ![Création d’un utilisateur de test Azure AD](./media/direct-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/direct-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="creating-a-direct-test-user"></a>Création d’un utilisateur de test direct

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans direct. Collaborez avec l’ [équipe du support technique de direct](https://direct4b.com/ja/support.html#inquiry)  pour ajouter des utilisateurs dans la plateforme directe. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à direct.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à direct, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **direct**.

    ![Configurer l'authentification unique](./media/direct-tutorial/tutorial_direct_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

1. Si vous souhaitez tester en **mode initié par IDP** :

    Lorsque vous cliquez sur la mosaïque **direct** dans le volet d’accès, vous devez être connecté automatiquement à votre application **direct**.

2. Si vous souhaitez tester en **mode initié par SP** :

    a. Cliquez sur la mosaïque **direct** dans le volet d’accès, et vous serez redirigé vers la page d’authentification de l’application.

    b. Entrez votre `subdomain` dans la zone de texte affichée et appuyez sur « 次へ (Suivant) ». Vous devriez être automatiquement connecté à votre application **direct**.

Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/direct-tutorial/tutorial_general_01.png
[2]: ./media/direct-tutorial/tutorial_general_02.png
[3]: ./media/direct-tutorial/tutorial_general_03.png
[4]: ./media/direct-tutorial/tutorial_general_04.png

[100]: ./media/direct-tutorial/tutorial_general_100.png

[200]: ./media/direct-tutorial/tutorial_general_200.png
[201]: ./media/direct-tutorial/tutorial_general_201.png
[202]: ./media/direct-tutorial/tutorial_general_202.png
[203]: ./media/direct-tutorial/tutorial_general_203.png
