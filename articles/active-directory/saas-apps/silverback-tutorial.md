---
title: 'Tutoriel : Intégration d’Azure Active Directory à Silverback | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172705"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutoriel : Intégration d’Azure Active Directory à Silverback

L’objectif de ce didacticiel est de vous apprendre à intégrer Silverback à Azure Active Directory (Azure AD).

L’intégration de Silverback dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Silverback.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Silverback (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Silverback, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Silverback actif

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Silverback à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-silverback-from-the-gallery"></a>Ajout de Silverback à partir de la galerie
Pour configurer l’intégration de Silverback à Azure AD, vous devez ajouter Silverback, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Silverback à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Silverback**, sélectionnez **Silverback** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Silverback dans la liste des résultats](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Silverback sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Silverback équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Silverback associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Silverback, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Silverback](#create-a-silverback-test-user)** pour avoir un équivalent de Britta Simon dans Silverback, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Silverback.

**Pour configurer l’authentification unique Azure AD avec Silverback, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Silverback**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Dans la section **Domaine et URL Silverback**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Silverback](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YOURSILVERBACKURL>.com/ssp`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<YOURSILVERBACKURL>.com`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Silverback](mailto:helpdesk@matrix42.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/silverback-tutorial/tutorial_general_400.png)

6.  Connectez-vous à votre serveur Silverback en tant qu’administrateur et procédez comme suit :

    a.  Accédez à **Administrateur** > **Fournisseur d’authentification**.

    b. Dans la page **Authentication Provider Settings** (Paramètres du fournisseur d’authentification), procédez comme suit :

    ![Administrateur ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Cliquez sur **Importer à partir d’une URL**.
    
    d.  Collez l’URL de métadonnées copiée et cliquez sur **OK**.
    
    e.  Confirmez avec **OK**, les valeurs sont remplies automatiquement.
    
    f.  Activez **Show on Login Page** (Afficher sur la page de connexion).
    
    g.  Activez **Dynamic User Creation** (Création dynamique de l’utilisateur) si vous souhaitez ajouter par utilisateurs autorisés Azure AD automatiquement (facultatif).
    
    h.  Créez un **titre** pour le bouton sur le portail libre-service.

    i.  Chargez une **icône** en cliquant sur **Choisir un fichier**.
    
    j.  Sélectionnez la **couleur** d’arrière-plan du bouton.
    
    k.  Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/silverback-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/silverback-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/silverback-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-silverback-test-user"></a>Créer un utilisateur de test Silverback

Pour permettre aux utilisateurs Azure AD de se connecter à Silverback, vous devez les approvisionner dans Silverback. Dans Silverback, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre serveur Silverback en tant qu’administrateur.

2. Accédez à **Utilisateurs** et **ajoutez un nouvel utilisateur de l’appareil**.

3. Dans la page **De base**, procédez comme suit :

    ![L’utilisateur ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Dans la zone de texte **Nom d’utilisateur**, entrez le nom d’un utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

    d. Dans la zone de texte **Adresse e-mail**, saisissez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    e. Dans la zone de texte **Mot de passe**, entrez votre mot de passe.
    
    f. Dans la zone de texte **Confirmer le mot de passe**, retapez votre mot de passe et confirmez.

    g. Cliquez sur **Enregistrer**.

>[!NOTE]
>Si vous ne souhaitez pas créer chaque utilisateur manuellement, cochez la case **Dynamic User Creation** (Création dynamique de l’utilisateur) sous **Administrateur** > **Fournisseur d’authentification**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Silverback.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Silverback, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Silverback**.

    ![Lien Silverback dans la liste des applications](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Silverback dans le volet d’accès, vous devez être connecté automatiquement à votre application Silverback.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

