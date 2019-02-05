---
title: 'Didacticiel : Intégration d’Azure Active Directory à AnswerHub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 41a1eef4ff417890114addcac00e2df3e49dc529
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453261"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Didacticiel : Intégration d’Azure Active Directory à AnswerHub

Dans ce didacticiel, vous allez apprendre à intégrer AnswerHub à Azure Active Directory (Azure AD).
L’intégration d’AnswerHub dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à AnswerHub.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à AnswerHub (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à AnswerHub, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement AnswerHub pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AnswerHub prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-answerhub-from-the-gallery"></a>Ajout d’AnswerHub à partir de la galerie

Pour configurer l’intégration d’AnswerHub à Azure AD, vous devez ajouter AnswerHub depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter AnswerHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **AnswerHub**, sélectionnez **AnswerHub** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![AnswerHub dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’AnswerHub sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur AnswerHub associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AnswerHub, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique AnswerHub](#configure-answerhub-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test AnswerHub](#create-answerhub-test-user)** pour avoir un équivalent de Britta Simon dans AnswerHub, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès d’AnswerHub, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **AnswerHub**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL AnswerHub](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<company>.answerhub.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<company>.answerhub.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’AnswerHub](mailto:success@answerhub.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer AnswerHub**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-answerhub-single-sign-on"></a>Configurer l’authentification unique AnswerHub

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AnswerHub en tant qu’administrateur.

    > [!NOTE]
    > Si vous avez besoin d’aide pour la configuration d’AnswerHub, contactez [l’équipe de support technique de AnswerHub](mailto:success@answerhub.com.).

2. Accédez à **Administration**.

3. Cliquez sur l’onglet **Users &amp; Groups** .

4. Dans le volet de navigation situé sur le côté gauche, dans la section **Social Settings**, cliquez sur **SAML Setup**.

5. Cliquez sur l’onglet **IDP Config** .

6. Sous l’onglet **IDP Config** , procédez comme suit :

    ![Configuration de SAML](./media/answerhub-tutorial/ic785172.png "Configuration de SAML")  
  
    a. Dans la zone de texte **IDP Login URL** (URL de connexion du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée depuis le portail Azure.
  
    b. Dans la zone de texte **IDP Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **IDP Name Identifier Format** (Format de l’identificateur du nom IDP), entrez la valeur de l’identificateur d’utilisateur telle que sélectionnée dans le portail Azure à la section **Attributs utilisateur**.
  
    d. Cliquez sur **Keys and Certificates**.

7. Sous l’onglet **Keys and Certificates**, effectuez les étapes suivantes :

    ![Clés et certificats](./media/answerhub-tutorial/ic785173.png "Clés et certificats")  

    a. Dans le Bloc-notes, ouvrez le certificat codé en base 64 que vous avez téléchargé depuis le portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IDP Public Key (x509 Format)** (Clé publique IDP au format x509).
  
    b. Cliquez sur **Enregistrer**.

8. Sous l’onglet **IDP Config**, cliquez sur **Save**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à AnswerHub.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **AnswerHub**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **AnswerHub**.

    ![Lien AnswerHub dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-answerhub-test-user"></a>Créer un utilisateur de test AnswerHub

Pour permettre aux utilisateurs Azure AD de se connecter à AnswerHub, vous devez les approvisionner dans AnswerHub. Dans le cas d’AnswerHub, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **AnswerHub** en tant qu’administrateur.

2. Accédez à **Administration**.

3. Cliquez sur l’onglet **Users & Groups**.

4. Dans le volet de navigation situé sur le côté gauche, dans la section **Manage Users**, cliquez sur **Create or import users**, puis sur **Users & Groups**.

   ![Utilisateurs et groupes](./media/answerhub-tutorial/ic785175.png "Utilisateurs et groupes")

5. Tapez l’adresse e-mail, le nom d’utilisateur et le mot de passe du compte d’utilisateur Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email address**, **Username** et **Password**, puis cliquez sur **Save**.

> [!NOTE]
> Vous pouvez utiliser n'importe quel autre outil ou API de création de compte d’utilisateur AnswerHub fourni par ce service pour approvisionner des comptes utilisateurs AAD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette AnswerHub dans le volet d’accès, vous devez vous connecter automatiquement à l’application AnswerHub pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

