---
title: 'Didacticiel : Intégration d’Azure Active Directory à Spotinst | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bffdf439a192fb10fe695fbfa18e8c7abf8077
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541886"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Didacticiel : Intégration d’Azure Active Directory à Spotinst

L’objectif de ce tutoriel est de vous montrer comment intégrer Spotinst à Azure Active Directory (Azure AD).

L’intégration de Spotinst à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Spotinst.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Spotinst (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Spotinst, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Spotinst pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Spotinst à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-spotinst-from-the-gallery"></a>Ajout de Spotinst à partir de la galerie
Pour configurer l’intégration de Spotinst à Azure AD, vous devez ajouter Spotinst, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Spotinst à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Spotinst**, sélectionnez **Spotinst** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Spotinst dans la liste des résultats](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Spotinst au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Spotinst équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Spotinst qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Spotinst, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Spotinst](#create-a-spotinst-test-user)** pour avoir un équivalent de Britta Simon dans Spotinst lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure, et vous configurez l’authentification unique dans votre application Spotinst.

**Pour configurer l’authentification unique Azure AD avec Spotinst, suivez ces étapes :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Spotinst**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Dans la section **Domaines et URL Spotinst**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par IDP :

    ![Informations d’authentification unique dans Domaine et URL Spotinst](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Cliquez sur **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **État de relais**, tapez une valeur : `<ID>`

    c. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, dans la zone de texte **URL de connexion**, tapez l’URL : `https://console.spotinst.com`

    > [!NOTE]
    > La valeur État de relais n’est pas la valeur réelle. Vous mettrez à jour la valeur de l’état de relais avec la valeur réelle en suivant les explications données plus loin dans le tutoriel.

4. L’application Spotinst attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configurer l'authentification unique](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.

    e. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/spotinst-tutorial/tutorial_general_400.png)

8. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Spotinst en tant qu’administrateur de la sécurité.

9. Cliquez sur **l’icône de l’utilisateur** dans le coin supérieur droit de l’écran et cliquez sur **Settings** (Paramètres).

    ![Paramètres de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Cliquez sur l’onglet **SECURITY** (SÉCURITÉ) en haut, puis sélectionnez **Identity Providers** (Fournisseurs d’identité) et suivez ces étapes :

    ![Sécurité de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copiez la valeur de **Relay State** (État de relais) et collez-la dans la zone de texte **État de relais** de la section **Domaine et URL Spotinst** dans le portail Azure.

    b. Cliquez sur **BROWSE** (PARCOURIR) pour charger le fichier xml de métadonnées que vous avez téléchargé du portail Azure.

    c. Cliquez sur **ENREGISTRER**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/spotinst-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/spotinst-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/spotinst-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-spotinst-test-user"></a>Créer un utilisateur de test Spotinst

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Spotinst.

1. Si vous avez configuré l’application en mode initié par le **fournisseur de service**, effectuez les étapes suivantes :

   a. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Spotinst en tant qu’administrateur de la sécurité.

   b. Cliquez sur **l’icône de l’utilisateur** dans le coin supérieur droit de l’écran et cliquez sur **Settings** (Paramètres).

    ![Paramètres de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Cliquez sur **Users** (Utilisateurs) et sélectionnez **ADD USER** (AJOUTER UN UTILISATEUR).

    ![Paramètres de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Dans la section d’ajout d’un utilisateur, procédez comme suit :

    ![Paramètres de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Dans la zone de texte **Full Name** (Nom complet), entrez le nom entier d’un utilisateur, par exemple **Britta Simon**.

    * Dans le **E-mail** zone de texte, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    * Sélectionnez les détails propres à votre organisation pour **Organization Role (Rôle de l’organisation), Account Role (Rôle de compte) et Accounts (Comptes)**.

2. Si vous avez configuré l’application en mode initié par **IDP**, vous n’avez aucune opération à effectuer dans cette section. Spotinst prend en charge le provisionnement juste-à-temps, option qui est activée par défaut. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Spotinst.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Spotinst.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Spotinst, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Spotinst**.

    ![Lien Spotinst dans la liste des applications](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Spotinst dans le Panneau d’accès doit vous connecter automatiquement à votre application Spotinst.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

