---
title: 'Didacticiel : Intégration d’Azure Active Directory à OrgChart Now | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: e23d76074f4b428b672e0cd5aeeaba99d080a4cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435932"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Didacticiel : Intégration d’Azure Active Directory à OrgChart Now

Dans ce didacticiel, vous allez apprendre à intégrer OrgChart Now dans Azure Active Directory (Azure AD).

L’intégration d’OrgChart Now dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OrgChart Now.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à OrgChart Now (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans OrgChart Now, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OrgChart Now avec authentification unique activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’OrgChart Now à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-orgchart-now-from-the-gallery"></a>Ajout d’OrgChart Now à partir de la galerie
Pour configurer l’intégration d’OrgChart Now dans Azure AD, vous devez ajouter OrgChart Now à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter OrgChart Now à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **OrgChart Now**, sélectionnez **OrgChart Now** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![OrgChart Now dans la liste des résultats](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec OrgChart Now et un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur correspondant dans OrgChart Now à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur correspondant dans OrgChart Now doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec OrgChart Now, vous devez suivre les étapes ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test OrgChart Now](#create-an-orgchart-now-test-user)** pour avoir un équivalent de Britta Simon dans OrgChart Now, lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application OrgChart Now.

**Pour configurer l’authentification unique Azure AD avec OrgChart Now, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **OrgChart Now**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Dans la section **Domaines et URL OrgChart Now**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    Dans la zone de texte **Identificateur**, tapez une URL : `https://sso2.orgchartnow.com`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` est l’ID d’entité SAML copié à partir de la section de référence rapide, décrite plus loin dans le didacticiel.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Dans la section **OrgChart Now Configuration** (Configuration d’OrgChart Now), cliquez sur **Configure OrgChart Now** (Configurer OrgChart Now) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML** à partir de la **section Référence rapide** et utilisez-le pour compléter l’**URL de connexion** dans la section **Domaine et URL OrgChart Now**.

    ![Configuration d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Pour configurer l’authentification unique du côté **OrgChart Now**, vous devez envoyer le **fichier XML de métadonnées** téléchargé à l’[équipe de support technique d’OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-orgchart-now-test-user"></a>Créer un utilisateur de test OrgChart Now

Pour permettre aux utilisateurs Azure AD de se connecter à OrgChart Now, vous devez les approvisionner dans OrgChart Now. 

1. OrgChart Now prend en charge l’approvisionnement juste-à-temps (option activée par défaut). Un utilisateur est créé lors d’une tentative d’accès à OrgChart Now (s’il n’existe pas déjà). La fonctionnalité d’approvisionnement d’utilisateur juste-à-temps crée un utilisateur **en lecture seule** lorsqu’une requête d’authentification unique provient d’un IDP reconnu et que l’adresse électronique dans l’assertion SAML ne se trouve pas dans la liste des utilisateurs. Pour cette fonctionnalité d’approvisionnement automatique, vous devez créer un groupe d’accès intitulé **Général** dans OrgChart Now. Suivez la procédure de création d’un groupe d’accès :

    a. Accédez à l’option **Gérer les groupes** après avoir cliqué sur l’**engrenage** dans l’angle supérieur droit de l’interface utilisateur.

    ![Groupes d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Sélectionnez l’icône **Ajouter** et attribuez au groupe le nom **Général**, puis cliquez sur **OK**. 

    ![Ajout dans OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Sélectionnez le ou les dossiers auxquels les utilisateurs généraux ou en lecture seule doivent avoir accès :

    ![Dossiers d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Verrouillez** les dossiers afin que seuls les utilisateurs administrateurs puissent les modifier. Ensuite, appuyez sur **OK**.

    ![Verrouillage d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Pour créer des utilisateurs **Admin** et des utilisateurs **en lecture/écriture**, vous devez créer manuellement un utilisateur afin d’obtenir l’accès à son niveau de privilège via l’authentification unique. Pour approvisionner un compte d’utilisateur, procédez comme suit :

    a. Connectez-vous à OrgChart Now en tant qu’administrateur de la sécurité.

    b.  Cliquez sur **Paramètres** sur l’angle supérieur droit, puis accédez à **Gérer les utilisateurs**.

    ![Paramètres d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Cliquez sur **Ajouter** et effectuez les opérations suivantes :

    ![Gestion d’OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Dans la zone de texte **ID utilisateur**, entrez l’ID d’utilisateur, par exemple **brittasimon@contoso.com**.

    * Dans la zone de texte **Adresse e-mail**, saisissez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    * Cliquez sur **Add**.
    
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OrgChart Now.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à OrgChart Now, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **OrgChart Now**.

    ![Lien OrgChart Now dans la liste des applications](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette OrgChart Now dans le volet d’accès, vous vous connectez automatiquement à votre application OrgChart Now.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

