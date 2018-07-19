---
title: 'Tutoriel : Intégration d’Azure Active Directory à iWellnessNow | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iWellnessNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24ffc841-7a77-481c-9cc4-6f8bda58fe66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 4f5ff9906ec5a4f57d64704a4b10cf7027878e72
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047074"
---
# <a name="tutorial-azure-active-directory-integration-with-iwellnessnow"></a>Tutoriel : Intégration d’Azure Active Directory à iWellnessNow

Dans ce tutoriel, vous allez apprendre à intégrer iWellnessNow à Azure Active Directory (Azure AD).

L’intégration d’iWellnessNow à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à iWellnessNow.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à iWellnessNow (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à iWellnessNow, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement iWellnessNow pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’iWellnessNow à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-iwellnessnow-from-the-gallery"></a>Ajout d’iWellnessNow à partir de la galerie
Pour configurer l’intégration d’iWellnessNow à Azure AD, vous devez ajouter iWellnessNow à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter iWellnessNow à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **iWellnessNow**, sélectionnez **iWellnessNow** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![iWellnessNow dans la liste des résultats](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iWellnessNow avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur iWellnessNow équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur iWellnessNow associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec iWellnessNow, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test iWellnessNow](#create-an-iwellnessnow-test-user)** pour avoir un équivalent de Britta Simon dans iWellnessNow qui soit associé à la représentation Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application iWellnessNow.

**Pour configurer l’authentification unique Azure AD avec iWellnessNow, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **iWellnessNow**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_samlbase.png)

3. Dans la section **Domaines et URL iWellnessNow**, si vous disposez du **fichier de métadonnées du fournisseur de services** et que vous souhaitez configurer l’application en Mode initié par **IDP**, procédez comme suit :

    ![Chargement de l’authentification unique dans Domaine et URL iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_upload.png)

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Configuration du chargement de l’authentification unique dans Domaine et URL iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_uploadconfig.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.
    
    c. Une fois le chargement du **fichier de métadonnées du fournisseur de services** terminé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement remplies dans la zone de texte de la section **Domaine et URL iWellnessNow**, comme indiqué ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url3.png)

4. Si vous ne disposez pas du **fichier de métadonnées du fournisseur de services** et que vous souhaitez configurer l’application en mode initié par **IDP**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `http://<CustomerName>.iwellnessnow.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<CustomerName>.iwellnessnow.com/ssologin`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CustomerName>.iwellnessnow.com/`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique d’iWellnessNow](mailto:info@iwellnessnow.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/iwellnessnow-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique du côté **iWellnessNow**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support technique iWellnessNow](mailto:info@iwellnessnow.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/iwellnessnow-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/iwellnessnow-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/iwellnessnow-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/iwellnessnow-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-iwellnessnow-test-user"></a>Créer un utilisateur de test iWellnessNow

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans iWellnessNow. Collaborez avec l’[équipe de support iWellnessNow](mailto:info@iwellnessnow.com) pour ajouter les utilisateurs à la plateforme iWellnessNow. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iWellnessNow.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à iWellnessNow, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **iWellnessNow**.

    ![Lien iWellnessNow dans la liste des applications](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque iWellnessNow dans le panneau d’accès, vous devriez être connecté automatiquement à votre application iWellnessNow.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iwellnessnow-tutorial/tutorial_general_01.png
[2]: ./media/iwellnessnow-tutorial/tutorial_general_02.png
[3]: ./media/iwellnessnow-tutorial/tutorial_general_03.png
[4]: ./media/iwellnessnow-tutorial/tutorial_general_04.png

[100]: ./media/iwellnessnow-tutorial/tutorial_general_100.png

[200]: ./media/iwellnessnow-tutorial/tutorial_general_200.png
[201]: ./media/iwellnessnow-tutorial/tutorial_general_201.png
[202]: ./media/iwellnessnow-tutorial/tutorial_general_202.png
[203]: ./media/iwellnessnow-tutorial/tutorial_general_203.png

