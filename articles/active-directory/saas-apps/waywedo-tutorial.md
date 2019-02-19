---
title: 'Tutoriel : Intégration d’Azure Active Directory à Way We Do | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc6d8e2cf7ac4786f30484325406a1fe696dff3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165125"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Tutoriel : Intégration d’Azure Active Directory à Way We Do

Dans ce tutoriel, vous apprenez à intégrer Way We Do à Azure Active Directory (Azure AD).

L’intégration de Way We Do à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Way We Do.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Way We Do (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Way We Do, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Way We Do pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Way We Do à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-way-we-do-from-the-gallery"></a>Ajout de Way We Do à partir de la galerie
Pour configurer l’intégration de Way We Do à Azure AD, vous devez ajouter Way We Do à partir de la galerie, dans votre liste d’applications SaaS gérées.

**Pour ajouter Way We Do à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Way We Do**, sélectionnez **Way We Do** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Way We Do dans la liste des résultats](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et tester l’authentification unique Azure AD avec Way We Do au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Way We Do équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Way We Do associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Way We Do, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Way We Do](#create-a-way-we-do-test-user)** pour avoir un équivalent de Britta Simon dans Way We Do lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Way We Do.

**Pour configurer l’authentification unique Azure AD avec Way We Do, suivez ces étapes :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Way We Do**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Dans la section **Domaine et URL Way We Do**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Way We Do](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Way We Do](mailto:support@waywedo.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/waywedo-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Way We Do**, cliquez sur **Configurer Way We Do** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Way We Do](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Way We Do en tant qu’administrateur de la sécurité.

8. Cliquez sur l’**icône représentant une personne** dans le coin supérieur droit d’une page de Way We Do, puis cliquez sur **Account** (Compte) dans le menu déroulant.

    ![Compte Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Cliquez sur l’**icône de menu** pour ouvrir le menu de navigation de commande, puis cliquez sur **Single Sign On** (Authentification unique).

    ![Authentification unique Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Dans la page **Single sign-on setup** (Configuration de l’authentification unique), procédez comme suit :

    ![Enregistrer Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Cliquez sur le bouton bascule de **Turn on single sign-on** (Activer l’authentification unique) pour choisir **Yes** (Oui) et ainsi activer l’authentification unique.

    b. Dans la zone de texte **Single sign-on name** (Nom de l’authentification unique), entrez votre nom.

    c. Dans la zone de texte **Entity ID** (ID d’entité), collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **SAML SSO URL** (URL SSO SAML), collez la valeur de **SAML Single Sign-On Service URL** (l’URL du service d’authentification unique SAML) que vous avez copiée à partir du portail Azure.

    e. Chargez le certificat en cliquant sur le bouton **Select** (sélectionner) situé en regard de **Certificate** (Certificat).

    f. **Paramètres facultatifs** -
    
    * Enable Passwords (Activer les mots de passe) - Lorsque cette option est désactivée, le mot de passe habituel fonctionne pour Way We Do de telle sorte que les utilisateurs n’ont pas d’autre choix que celui d’utiliser l’authentification unique.

    * Enable Auto-provisioning (Activer le provisionnement automatique) - Lorsque cette option est activée, l’adresse e-mail utilisée pour l’authentification est automatiquement comparée à la liste des utilisateurs dans Way We Do. Si l’adresse e-mail ne correspond pas à un utilisateur actif dans Way We Do, un nouveau compte d’utilisateur pour la personne se connectant est ajouté, dans lequel il lui est demandé de renseigner les informations manquantes.

      > [!NOTE]
      > Les utilisateurs ajoutés via l’authentification unique sont ajoutés en tant qu’utilisateurs généraux et aucun rôle ne leur est attribué dans le système. Un administrateur est en mesure d’accéder et de modifier son rôle de sécurité en tant qu’éditeur ou administrateur, il peut également affecter un ou plusieurs rôles d’organigramme. 

    g. Cliquez sur **Save** pour enregistrer vos paramètres.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/waywedo-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/waywedo-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/waywedo-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/waywedo-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-way-we-do-test-user"></a>Créer un utilisateur de test Way We Do

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Way We Do. Way We Do prend en charge le provisionnement juste-à-temps, une option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Way We Do.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez  [l’équipe de support technique du client Way We Do](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Way We Do.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Way We Do, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Way We Do**.

    ![Lien Way We Do dans la liste des applications](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Way We Do dans le panneau d’accès doit automatiquement vous connecter à votre application Way We Do.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

