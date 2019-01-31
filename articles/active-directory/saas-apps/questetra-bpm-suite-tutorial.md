---
title: "Tutoriel : Intégration d'Azure Active Directory à Questetra BPM Suite | Microsoft Docs"
description: Découvrez comment configurer l'authentification unique entre Azure Active Directory et Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 72932687df5efd09249bd144bf048b23d7c39f4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150937"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutoriel : Intégration d'Azure Active Directory avec Questetra BPM Suite

Dans ce didacticiel, vous allez apprendre à intégrer Questetra BPM Suite dans Azure Active Directory (Azure AD).

L’intégration de Questetra BPM Suite à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Questetra BPM Suite.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Questetra BPM Suite (via l'authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l'intégration d'Azure AD avec Questetra BPM Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Questetra BPM Suite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Questetra BPM Suite à partir de la galerie
1. Configurer et tester l’authentification unique Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Ajouter Questetra BPM Suite à partir de la galerie
Pour configurer l'intégration de Questetra BPM Suite avec Azure AD, vous devez ajouter Questetra BPM Suite, disponible dans la galerie, à votre liste d'applications SaaS gérées.

**Pour ajouter Questetra BPM Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Questetra BPM Suite**, sélectionnez **Questetra BPM Suite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Ajouter à partir de la galerie](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Questetra BPM Suite, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Questetra BPM Suite équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Questetra BPM Suite associé doit être établi.

Dans Questetra BPM Suite, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l'authentification unique Azure AD avec Questetra BPM Suite, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)** pour avoir un équivalent de Britta Simon dans Questetra BPM Suite qui soit lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Questetra BPM Suite.

**Pour configurer l'authentification unique Azure AD avec Questetra BPM Suite, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Questetra BPM Suite**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Authentification basée sur SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. Dans la section **Domaine et URL Questetra BPM Suite**, effectuez les étapes suivantes :

    ![Section Domaine et URL Questetra BPM Suite](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Vous pouvez obtenir ces valeurs dans la section **Informations SP** de votre site d’entreprise **Questetra BPM Suite**, décrit plus loin dans le didacticiel ou contacter l’[équipe de support Questetra BPM Suite](https://www.questetra.com/contact/). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Section Certificat de signature SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Questetra BPM Suite**, cliquez sur **Configurer Questetra BPM Suite** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Section Configuration de Questetra BPM Suite](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d'entreprise **Questetra BPM Suite** en tant qu'administrateur.

1. Dans le menu situé en haut, cliquez sur **Paramètres système**. 
   
    ![Authentification unique Azure AD][10]

1. Pour ouvrir la page **SingleSignOnSAML**, cliquez sur **SSO (SAML)**. 
   
    ![Authentification unique Azure AD][11]

1. Sur votre site d’entreprise **Questetra BPM Suite**, dans la section **Informations SP**, effectuez les étapes suivantes :

    a. Copiez l’**URL ACS**, puis collez-la dans la zone de texte **URL de connexion** dans la section **Domaine et URL Questetra BPM Suite** à partir du portail Azure.
    
    b. Copiez l’**ID d’entité**, puis collez-le dans la zone de texte **Identificateur** dans la section **Domaine et URL Questetra BPM Suite** à partir du portail Azure.

1. Sur votre site d’entreprise **Questetra BPM Suite**, effectuez les étapes suivantes : 
   
    ![Configurer l'authentification unique][15]
   
    a. Sélectionnez **Activer l'authentification unique**.
   
    b. Dans la zone de texte **ID d’entité**, collez la valeur de l’**ID d’entité SAML** que vous avez copiée à partir du portail Azure.
    
    c. Dans la zone de texte **URL de la page de connexion**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
    
    d. Dans la zone de texte **URL de la page de déconnexion**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
    
    e. Dans la zone de texte **Format NameID**, type `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Ouvrez, dans le Bloc-notes, votre certificat codé en **base 64**, téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de validation**. 

    g. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Créer un utilisateur de test Questetra BPM Suite

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Questetra BPM Suite.

**Pour créer un utilisateur appelé Britta Simon dans Questetra BPM Suite, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Questetra BPM Suite en tant qu’administrateur.
1. Accédez à **Paramètres système > Liste des utilisateurs > Nouvel utilisateur**. 
1. Dans la boîte de dialogue Nouvel utilisateur, procédez comme suit : 
   
    ![Créer un utilisateur de test][300] 
   
    a. Dans la zone de texte **Nom**, tapez le **nom** de l’utilisateur **britta.simon@contoso.com**.
   
    b. Dans la zone de texte **E-mail**, entrez l’**e-mail** de l’utilisateur **britta.simon@contoso.com**.
   
    c. Dans la zone de texte **Mot de passe**, tapez un **mot de passe** pour l’utilisateur.
    
    d. Cliquez sur **Ajouter un nouvel utilisateur**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Questetra BPM Suite.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à Questetra BPM Suite, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Questetra BPM Suite**.

    ![Questetra BPM Suite dans la liste des applications](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Questetra BPM Suite dans le volet d'accès, vous devez être connecté automatiquement à votre application Questetra BPM Suite.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

