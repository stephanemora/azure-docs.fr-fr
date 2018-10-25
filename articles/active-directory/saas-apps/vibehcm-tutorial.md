---
title: 'Didacticiel : Intégration d’Azure Active Directory à Vibe HCM | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Vibe HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4379bef7-adc9-4b6d-9384-c46d9a914bfe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: jeedes
ms.openlocfilehash: 395f99c986e0a51e8dd2c440d258724e862f275c
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020609"
---
# <a name="tutorial-azure-active-directory-integration-with-vibe-hcm"></a>Didacticiel : Intégration d’Azure Active Directory à Vibe HCM

Dans ce didacticiel, vous allez apprendre à intégrer Vibe HCM à Azure Active Directory (Azure AD).

L’intégration de Vibe HCM à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Vibe HCM.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Vibe HCM (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Vibe HCM, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique Vibe HCM est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Vibe HCM à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-vibe-hcm-from-the-gallery"></a>Ajout de Vibe HCM à partir de la galerie
Pour configurer l’intégration de Vibe HCM à Azure AD, vous devez ajouter Vibe HCM à votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter Vibe HCM à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **Vibe HCM**, sélectionnez **Vibe HCM** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour l’ajouter l’application.

    ![Vibe HCM dans la liste des résultats](./media/vibehcm-tutorial/tutorial_vibehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Vibe HCM sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Vibe HCM équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Vibe HCM associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Vibe HCM, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Vibe HCM](#create-a-vibe-hcm-test-user)** pour avoir un équivalent de Britta Simon dans Vibe HCM lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Vibe HCM.

**Pour configurer l’authentification unique Azure AD avec Vibe HCM, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Vibe HCM**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/vibehcm-tutorial/tutorial_vibehcm_samlbase.png)

3. Dans la section **Domaine et URL Vibe HCM**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure :

    ![Informations d’authentification unique dans pour le domaine et l’URL Vibe HCM](./media/vibehcm-tutorial/tutorial_vibehcm_url.png)

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans pour le domaine et l’URL Vibe HCM](./media/vibehcm-tutorial/tutorial_vibehcm_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyName>.vibehcm.com/portal.jsp`
     
    > [!NOTE] 
    > La valeur d’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe du support technique Vibe HCM](mailto:support@vibehcm.com) pour obtenir la valeur.
 
4. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/vibehcm-tutorial/tutorial_vibehcm_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/vibehcm-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **Vibe HCM**, vous devez envoyer l’**URL des métadonnées de fédération de l’application** copiée à l’[équipe du support technique Vibe HCM](mailto:support@vibehcm.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/vibehcm-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/vibehcm-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/vibehcm-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/vibehcm-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-vibe-hcm-test-user"></a>Créer un utilisateur de test Vibe HCM

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Vibe HCM. Collaborez avec l’[équipe du support technique Vibe HCM](mailto:support@vibehcm.com) pour ajouter des utilisateurs à la plateforme Vibe HCM. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Vibe HCM.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Vibe HCM, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Vibe HCM**.

    ![Lien Vibe HCM dans la liste des applications](./media/vibehcm-tutorial/tutorial_vibehcm_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Vibe HCM dans le volet d’accès, vous devez être connecté automatiquement à votre application Vibe HCM.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vibehcm-tutorial/tutorial_general_01.png
[2]: ./media/vibehcm-tutorial/tutorial_general_02.png
[3]: ./media/vibehcm-tutorial/tutorial_general_03.png
[4]: ./media/vibehcm-tutorial/tutorial_general_04.png

[100]: ./media/vibehcm-tutorial/tutorial_general_100.png

[200]: ./media/vibehcm-tutorial/tutorial_general_200.png
[201]: ./media/vibehcm-tutorial/tutorial_general_201.png
[202]: ./media/vibehcm-tutorial/tutorial_general_202.png
[203]: ./media/vibehcm-tutorial/tutorial_general_203.png

