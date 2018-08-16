---
title: 'Didacticiel : Intégration d’Azure Active Directory à Ziflow | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 460a52f240f6b3723f93e81a11a8cd1ccc6c30c9
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626691"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Didacticiel : Intégration d’Azure Active Directory à Ziflow

Dans ce didacticiel, vous allez apprendre à intégrer Ziflow dans Azure Active Directory (Azure AD).

L’intégration de Ziflow dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Ziflow.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Ziflow (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD dans Ziflow, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Ziflow pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Ziflow depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ziflow-from-the-gallery"></a>Ajout de Ziflow depuis la galerie
Pour configurer l’intégration de Ziflow dans Azure AD, vous devez ajouter Ziflow depuis la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Ziflow à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Ziflow**, sélectionnez **Ziflow** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Ziflow dans la liste des résultats](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Ziflow et un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur correspondant dans Ziflow à un utilisateur Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur associé dans Ziflow doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Ziflow, vous devez suivre les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Ziflow](#create-a-ziflow-test-user)** pour avoir un équivalent de Britta Simon dans Ziflow lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Ziflow.

**Pour configurer l’authentification unique Azure AD avec Ziflow, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Ziflow**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Dans la section **Domaine et URL Ziflow**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Ziflow](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas réelles. Vous allez remplacer l’ID unique dans Identificateur et URL de connexion par la valeur réelle. La procédure est expliquée plus loin dans le didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/ziflow-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Ziflow**, cliquez sur **Configurer Ziflow** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Ziflow en tant qu’administrateur de la sécurité.

8. Dans l’angle supérieur droit, cliquez sur Avatar, puis cliquez sur **Gérer le compte**.

    ![Configuration de Ziflow - Gestion](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. Dans le coin supérieur gauche, cliquez sur **Authentification unique**.

    ![Configuration de Ziflow - Connexion](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Sur la page **Authentification unique**, effectuez les opérations suivantes :

    ![Configuration de Ziflow - Authentification unique](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Sélectionnez **Type** dans **SAML2.0**.

    b. Dans la zone de texte **URL de connexion**, collez la valeur d’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Téléchargez le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure, dans le **Certificat de signature X509**.

    d. Dans la zone de texte **URL de déconnexion**, collez la valeur de **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la section **Configuration Settings for your Identifier Provider (Paramètres de configuration de votre fournisseur d’identificateur)**, copiez l’ID unique en surbrillance et ajoutez-le à la fin de l’identificateur et de l’URL de connexion dans la section **Domaine et URL de Ziflow** sur le portail Azure.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/ziflow-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/ziflow-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/ziflow-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-ziflow-test-user"></a>Créer un utilisateur de test Ziflow

Pour se connecter à Ziflow, les utilisateurs d’Azure AD doivent être approvisionnés dans Ziflow. Dans Ziflow, l’approvisionnement est une tâche manuelle.

Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à Ziflow en tant qu’administrateur de la sécurité.

2. Accédez à **Personnes** en haut.

    ![Configuration de Ziflow - Personnes](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Cliquez sur **Ajouter**, puis sur **Continuer**.

    ![Configuration de Ziflow - Ajout d’utilisateur](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Dans la section **Add a User (Ajouter un utilisateur)**, procédez comme suit :

    ![Configuration de Ziflow - Ajout d’utilisateur](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, comme brittasimon@contoso.com.

    b. Dans la zone de texte **Prénom**, saisissez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom**, saisissez le nom de famille de l’utilisateur, par exemple Simon.

    d. Sélectionnez votre rôle Ziflow.

    e. Cliquez sur **Add 1 user (Ajouter un utilisateur)**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ziflow.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Ziflow, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Ziflow**.

    ![Lien Ziflow dans la liste des applications](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Ziflow dans le volet d’accès, vous vous connectez automatiquement à votre application Ziflow.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

