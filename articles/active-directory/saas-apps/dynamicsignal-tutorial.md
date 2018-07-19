---
title: 'Didacticiel : Intégration d’Azure Active Directory à Dynamic Signal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dynamic Signal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 863f7340-b065-4f59-b092-daa67da6f703
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 7a8b0387ebda574da572ac738bf3b75ef882224c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053123"
---
# <a name="tutorial-azure-active-directory-integration-with-dynamic-signal"></a>Didacticiel : Intégration d’Azure Active Directory à Dynamic Signal

Dans ce didacticiel, vous allez apprendre à intégrer Dynamic Signal à Azure Active Directory (Azure AD).

L’intégration de Dynamic Signal à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Dynamic Signal.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Dynamic Signal (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Dynamic Signal, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Dynamic Signal pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Dynamic Signal à partir de la gallerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-dynamic-signal-from-the-gallery"></a>Ajout de Dynamic Signal à partir de la gallerie
Pour configurer l’intégration de Dynamic Signal à Azure AD, vous devez ajouter Dynamic Signal disponible dans la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Dynamic Signal à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Dynamic Signal**, sélectionnez **Dynamic Signal** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Dynamic Signal dans la liste des résultats](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Dynamic Signal avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Dynamic Signal équivalent dans Azure AD. En d’autres termes, une relation de lien entre un utilisateur Azure AD et un utilisateur Dynamic Signal associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Dynamic Signal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Dynamic Signal](#create-a-dynamic-signal-test-user)** pour avoir un équivalent de Britta Simon dans Dynamic Signal lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Dynamic Signal.

**Pour configurer l’authentification unique Azure AD avec Dynamic Signal, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Dynamic Signal**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_samlbase.png)

3. Dans la section **Domaine et URL Dynamic Signal**, procédez comme suit :
 
    ![Informations d’authentification unique dans Domaine et URL Dynamic Signal](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.voicestorm.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.voicestorm.com`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.voicestorm.com/User/SsoResponse`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Dynamic Signal](mailto:support@dynamicsignal.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/dynamicsignal-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de Dynamic Signal**, cliquez sur **Configurer Dynamic Signal** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Dynamic Signal](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_configure.png) 

7. Pour configurer l’authentification unique côté **Dynamic Signal**, vous devez envoyer le **Certificat (Base64) téléchargé, l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à l’[équipe de support Dynamic Signal](mailto:support@dynamicsignal.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/dynamicsignal-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/dynamicsignal-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/dynamicsignal-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/dynamicsignal-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-dynamic-signal-test-user"></a>Créer un utilisateur de test Dynamic Signal

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Dynamic Signal. Dynamic Signal prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé durant une tentative d’accès à Dynamic Signal.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Dynamic Signal](mailto:support@dynamicsignal.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dynamic Signal.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Dynamic Signal, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Dynamic Signal**.

    ![Lien Dynamic Signal dans la liste des applications](./media/dynamicsignal-tutorial/tutorial_dynamicsignal_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Dynamic Signal dans le volet d’accès, vous devez être connecté automatiquement à votre application Dynamic Signal.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dynamicsignal-tutorial/tutorial_general_01.png
[2]: ./media/dynamicsignal-tutorial/tutorial_general_02.png
[3]: ./media/dynamicsignal-tutorial/tutorial_general_03.png
[4]: ./media/dynamicsignal-tutorial/tutorial_general_04.png

[100]: ./media/dynamicsignal-tutorial/tutorial_general_100.png

[200]: ./media/dynamicsignal-tutorial/tutorial_general_200.png
[201]: ./media/dynamicsignal-tutorial/tutorial_general_201.png
[202]: ./media/dynamicsignal-tutorial/tutorial_general_202.png
[203]: ./media/dynamicsignal-tutorial/tutorial_general_203.png

