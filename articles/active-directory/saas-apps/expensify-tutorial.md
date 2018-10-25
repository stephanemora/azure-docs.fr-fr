---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Expensify | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248038"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Didacticiel : Intégration d’Azure Active Directory avec Expensify

Dans ce didacticiel, vous allez apprendre à intégrer Expensify avec Azure Active Directory (Azure AD).

L’intégration d’Expensify avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Expensify.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Expensify (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Expensify, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Expensify pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Expensify à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-expensify-from-the-gallery"></a>Ajout d’Expensify à partir de la galerie

Pour configurer l’intégration de Expensify à Azure AD, vous devez ajouter Expensify à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Expensify à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Expensify**, sélectionnez **Expensify** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Expensify avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Expensify équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Expensify associé doit être établie.

Dans Expensify, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Expensify, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Expensify](#create-an-expensify-test-user)** pour avoir un équivalent de Britta Simon dans Expensify lié à la représentation Azure AD de l’utilisateur.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Expensify.

**Pour configurer l’authentification unique Azure AD avec Expensify, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Expensify**, cliquez sur **Authentification unique**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://www.expensify.com/authentication/saml/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Remplacez la section <companyname> de l’URL d’identificateur par le domaine de votre entreprise. Consultez l’exemple `https://contoso.expensify.com` ci-dessous. Dans Expensify, il s’agit du nom de votre domaine, tel qu’affiché dans **Paramètres > Contrôle de domaine**.

    ![Informations sur le domaine Expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le certificat correspondant à vos besoins et enregistrez-le sur votre ordinateur.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Pour activer l’authentification unique dans Expensify, vous devez d’abord activer le **contrôle de domaine** dans l’application. Vous pouvez activer le contrôle de domaine dans l’application via la procédure répertoriée [ici](http://help.expensify.com/domain-control). Pour une assistance supplémentaire, travaillez avec [l’équipe de support technique Expensify](mailto:help@expensify.com). Une fois que le contrôle de domaine est activé, suivez les étapes ci-dessous :
   
    ![Configurer l'authentification unique](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Connectez-vous à votre application Expensify.
    
    b. Dans le panneau gauche, cliquez sur **Paramètres** et accédez à **SAML**.
    
    c. Paramétrez l’option **Connexion SAML** sur **Activée**.
    
    d. Dans le Bloc-notes, ouvrez les métadonnées de fédération téléchargées à partir d’Azure AD, copiez le contenu et collez-le dans la zone de texte **Identity Provider Metadata** (Métadonnées du fournisseur d’identité).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-expensify-test-user"></a>Créer un utilisateur de test Expensify

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Expensify. Travaillez avec [l’équipe de support technique Expensify](mailto:help@expensify.com) pour ajouter les utilisateurs à la plateforme Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Expensify.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Expensify dans le volet d’accès, vous devriez être connecté automatiquement à votre application Expensify.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)




