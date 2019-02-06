---
title: 'Tutoriel : Intégration d’Azure Active Directory à Riskware | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.openlocfilehash: 3b4c979bf03b23280c9389a043375f088624efe6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163243"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutoriel : Intégration d’Azure Active Directory à Riskware

Dans ce tutoriel, vous allez apprendre à intégrer Riskware à Azure Active Directory (Azure AD).

L’intégration de Riskware dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Riskware.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Riskware (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Riskware, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Riskware pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Riskware à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-riskware-from-the-gallery"></a>Ajout de Riskware à partir de la galerie
Pour configurer l’intégration de Riskware à Azure AD, vous devez ajouter Riskware à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Riskware à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Riskware**, sélectionnez **Riskware** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Riskware dans la liste des résultats](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Riskware, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Riskware équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Riskware associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Riskware, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Riskware](#create-a-riskware-test-user)** pour avoir dans Riskware un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Riskware.

**Pour configurer l’authentification unique Azure AD avec Riskware, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Riskware**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. Dans la section **Domaine et URL Riskware**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Riskware](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | Environnement| Modèle d’URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Dans la zone de texte **Identificateur (ID d’entité)**, tapez une URL :
    | Environnement| Modèle d’URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique de Riskware](mailto:support@pansoftware.com.au).

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/riskware-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Riskware**, cliquez sur **Configurer Riskware** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Riskware en tant qu’administrateur.

1. Dans le coin supérieur droit, cliquez sur **Maintenance** pour ouvrir la page de maintenance.

    ![Configuration de Riskware - Maintenance](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Dans la page de maintenance, cliquez sur **Authentication**.

    ![Configuration de Riskware - Authentification](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Dans la page **Authentication Configuration**, effectuez les étapes suivantes :

    ![Configuration de Riskware - Configuration de l’authentification](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Sélectionnez **SAML** comme **Type** d’authentification.

    b. Dans la zone de texte **Code**, tapez votre code, par exemple AZURE_UAT.

    c. Dans la zone de texte **Description**, tapez votre description, par exemple Configuration d’Azure pour l’authentification unique.

    d. Dans la zone de texte de la page **Single Sign On Page** (Page d’authentification unique), collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Sign out Page** (Page de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans la zone de texte **Post Form Field** (Champ de formulaire de notification), tapez le nom du champ présent dans Post Response qui contient SAML, comme SAMLResponse.

    g. Dans la zone de texte **XML Identity Tag Name**, tapez l’attribut qui contient l’identificateur unique dans la réponse SAML, comme NameID.

    h. Dans le Bloc-notes, ouvrez le  **XML de métadonnées**  téléchargé du portail Azure, copiez le certificat à partir du fichier de métadonnées, puis collez-le dans la zone de texte **Certificate**.

    i. Dans la zone de texte **Consumer URL**, collez la valeur de **Reply URL**, que vous obtenez à partir de l’équipe de support.

    j. Dans la zone de texte **Issuer**, collez la valeur de **Identifier**, que vous obtenez à partir de l’équipe de support.

    > [!Note]
    > Pour obtenir ces valeurs, contactez l’[équipe de support technique de Riskware](mailto:support@pansoftware.com.au).

    k. Cochez la case **Use POST** (Utiliser POST).

    l. Cochez la case **Use SAML Request** (Utiliser une requête SAML).

    m. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/riskware-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/riskware-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/riskware-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-riskware-test-user"></a>Créer un utilisateur de test Riskware

Pour se connecter à Riskware, les utilisateurs Azure AD doivent être provisionnés dans Riskware. Dans Riskware, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Riskware en tant qu’administrateur de la sécurité.

1. Dans le coin supérieur droit, cliquez sur **Maintenance** pour ouvrir la page de maintenance. 

    ![Configuration de Riskware - Maintenance](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Dans la page de maintenance, cliquez sur **People**.

    ![Configuration de Riskware - Personnes](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Sélectionnez l’onglet **Details** (Détails), puis effectuez les étapes suivantes :

    ![Configuration de Riskware - Détails](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Sélectionnez Employee comme **Person Type**.

    b. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Surname**, tapez le nom de l’utilisateur, par exemple **Simon**.

1. Sous l’onglet **Security** , procédez comme suit :

    ![Configuration de Riskware - Sécurité](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Dans la section **Authentication**, sélectionnez le mode **Authentication**, que vous avez configuré comme Configuration d’Azure pour l’authentification unique.

    b. Dans la section **Logon Details**, dans la zone de texte **User ID**, entrez l’adresse e-mail d’un utilisateur comme **brittasimon@contoso.com**.

    c. Dans la zone de texte **Password**, tapez le mot de passe de l’utilisateur.

1. Sous l’onglet **Organization** (Organisation), effectuez les étapes suivantes :

    ![Configuration de Riskware - Organisation](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Sélectionnez l’option**Level 1** (Niveau 1) pour l’organisation.

    b. Dans la section **Person’s Primary Workplace**, dans la zone de texte **Location**, tapez votre emplacement.

    c. Dans la section **Employee**, sélectionnez Casual comme **Employee Status**.

1. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Riskware.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Riskware, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Riskware**.

    ![Lien Riskware dans la liste des applications](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette Riskware dans le volet d’accès, vous devez être connecté automatiquement à votre application Riskware.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

