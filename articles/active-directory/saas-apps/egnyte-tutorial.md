---
title: 'Didacticiel : Intégration d’Azure Active Directory à Egnyte | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7201b7d3f9f7e8333b18a087f58b6211101b1304
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233554"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Didacticiel : Intégration d’Azure Active Directory à Egnyte

Dans ce didacticiel, vous allez apprendre à intégrer Egnyte à Azure Active Directory (Azure AD).
L’intégration d’Egnyte dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Egnyte.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Egnyte (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Egnyte, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Egnyte pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Egnyte prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-egnyte-from-the-gallery"></a>Ajout d’Egnyte à partir de la galerie

Pour configurer l’intégration d’Egnyte avec Azure AD, vous devez ajouter Egnyte à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Egnyte à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Egnyte**, sélectionnez **Egnyte** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Egnyte dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Egnyte, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez associer l’utilisateur Azure AD à l’utilisateur Egnyte.

Pour configurer et tester l’authentification unique Azure AD avec Egnyte, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Egnyte](#configure-egnyte-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Egnyte](#create-egnyte-test-user)** pour obtenir un équivalent de Britta Simon dans Egnyte qui soit lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Egnyte, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Egnyte**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL d’Egnyte](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

5. Dans la section **Configurer Egnyte**, copiez l’URL appropriée en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-egnyte-single-sign-on"></a>Configurer l’authentification unique Egnyte

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Egnyte en tant qu’administrateur.

2. Cliquez sur **Settings**.
   
    ![Paramètres](./media/egnyte-tutorial/ic787819.png "Paramètres")

3. Dans le menu, cliquez sur **Settings**.

    ![Paramètres](./media/egnyte-tutorial/ic787820.png "Paramètres")

4. Cliquez sur l’onglet **Configuration** puis sur **Security**.

    ![Sécurité](./media/egnyte-tutorial/ic787821.png "Sécurité")

5. Dans la section **Single Sign-On Authentication** , procédez comme suit :

    ![Authentification unique](./media/egnyte-tutorial/ic787822.png "Authentification unique")   
    
    a. Pour **Single sign-on authentication**, sélectionnez **SAML 2.0**.
   
    b. Pour **Identity provider**, sélectionnez **AzureAD**.
   
    c. Collez la valeur **URL de connexion** copiée à partir du portail Azure dans la zone de texte **Identity provider login URL** (URL de connexion du fournisseur d’identité).
   
    d. Collez la valeur **Identificateur Azure AD** copiée à partir du portail Azure dans la zone de texte **Identity provider entity ID** (ID d’entité du fournisseur d’identité).
      
    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de fournisseur d’identité**.
   
    f. Pour **Default user mapping**, sélectionnez **Email address**.
   
    g. Pour **Use domain-specific issuer value**, sélectionnez **disabled**.
   
    h. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Egnyte.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Egnyte**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Egnyte**.

    ![Lien Egnyte dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-egnyte-test-user"></a>Créer un utilisateur de test Egnyte

Pour se connecter à Egnyte, les utilisateurs d’Azure AD doivent être approvisionnés dans Egnyte. Dans le cas de Egnyte, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Egnyte** en tant qu’administrateur.

2. Accédez à **Settings \> Users & Groups**.

3. Cliquez sur **Add New User**, puis sélectionnez le type d’utilisateur à ajouter.
   
    ![Utilisateurs](./media/egnyte-tutorial/ic787824.png "Utilisateurs")

4. Dans la section **New Power User** (Nouvel utilisateur avancé), effectuez les étapes suivantes :
    
    ![Nouvel utilisateur standard](./media/egnyte-tutorial/ic787825.png "New Standard User")   

    a. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    b. Dans la zone de texte **Username** (Nom d’utilisateur), entrez le nom d’un utilisateur, par exemple **Brittasimon**.

    c. Pour **Single sign-on** (Authentification unique), sélectionnez **Authentication Type** (Type d’authentification).
   
    d. Cliquez sur **Enregistrer**.
    
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory recevra une notification par courrier électronique.
    >

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur Egnyte fourni par Egnyte pour provisionner des comptes d’utilisateur Azure AD.
>

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Egnyte dans le volet d’accès, vous devez être connecté automatiquement à l’application Egnyte pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

