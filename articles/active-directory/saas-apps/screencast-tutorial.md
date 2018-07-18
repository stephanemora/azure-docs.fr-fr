---
title: 'Tutoriel : intégration d’Azure Active Directory à Screencast-O-Matic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: 7212e0b07b525286f0b194a53c6780269630ad9c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323594"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Tutoriel : intégration d’Azure Active Directory à Screencast-O-Matic

Dans ce tutoriel, vous allez apprendre à intégrer Screencast-O-Matic à Azure Active Directory (Azure AD).

L’intégration de Screencast-O-Matic à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Screencast-O-Matic.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Screencast-O-Matic (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Screencast-O-Matic, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Screencast-O-Matic pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Screencast-O-Matic à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Ajout de Screencast-O-Matic à partir de la galerie
Pour configurer l’intégration de Screencast-O-Matic dans Azure AD, vous devez ajouter Screencast-O-Matic depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Screencast-O-Matic à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Screencast-O-Matic**, sélectionnez **Screencast-O-Matic** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Screencast-O-Matic dans la liste des résultats](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Screencast-O-Matic, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Screencast-O-Matic équivalent dans Azure AD. En d’autres termes, une relation de lien entre un utilisateur Azure AD et un utilisateur Screencast-O-Matic associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Screencast-O-Matic, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Screencast-O-Matic](#create-a-screencast-o-matic-test-user)** pour avoir un équivalent de Britta Simon dans Screencast-O-Matic qui est lié à la représentation de l’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Screencast-O-Matic.

**Pour configurer l’authentification unique Azure AD avec Screencast-O-Matic, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Screencast-O-Matic**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Dans la section **Domaine et URL Screencast-O-Matic**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Screencast-O-Matic](./media/screencast-tutorial/tutorial_screencast_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique de Screencast-O-Matic](mailto:support@screencast-o-matic.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/screencast-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à Screencast-O-Matic en tant qu’administrateur.

7. Cliquez sur **Abonnement**.

    ![L’abonnement](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. Dans la section **Page d’accès**, cliquez sur **Configuration**.

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Dans la **page Configurer l’accès**, procédez comme suit :

    * Dans la section **URL d’accès**, tapez le nom de votre instance dans la zone de texte spécifiée.

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Sélectionnez **Demander un utilisateur de domaine** dans la section **Restriction d’utilisateur SAML (facultatif)**.

    * Sous **Charger le fichier XML des métadonnées IDP**, cliquez sur **Sélectionner le fichier** pour charger les métadonnées que vous avez téléchargées à partir du portail Azure.

    * Cliquez sur **OK**. 

    ![L’accès](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/screencast-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/screencast-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/screencast-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/screencast-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Créer un utilisateur de test Screencast-O-Matic

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Screencast-O-Matic. Screencast-O-Matic prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Screencast-O-Matic s’il n’existe pas déjà.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support client Screencast-O-Matic](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Screencast-O-Matic.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Screencast-O-Matic, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Screencast-O-Matic**.

    ![Lien Screencast-O-Matic dans la liste des applications](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Screencast-O-Matic dans le volet d’accès, vous devez être connecté automatiquement à votre application Screencast-O-Matic.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

