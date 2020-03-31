---
title: 'Tutoriel : Intégration d’Azure Active Directory à Mitel Connect | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160541"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutoriel : Intégration d’Azure Active Directory à Mitel MiCloud Connect

Dans ce tutoriel, vous allez apprendre à intégrer Mitel MiCloud Connect à Azure Active Directory (Azure AD). L’intégration de MiCloud Connect à Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès aux applications MiCloud Connect à l’aide de ses informations d’identification d’entreprise.
* Vous pouvez permettre aux utilisateurs de votre compte de se connecter automatiquement à MiCloud Connect (par le biais de l’authentification unique) avec leur compte Azure AD.


Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de MiCloud Connect à Azure AD, vous avez besoin des éléments suivants :

* Un abonnement Azure AD

  Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un compte Mitel MiCloud Connect

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD.

* Mitel Connect prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-mitel-connect-from-the-gallery"></a>Ajout de Mitel Connect à partir de la galerie

Pour configurer l’intégration de Mitel Connect à Azure AD, vous devez ajouter Mitel Connect, à partir de la galerie, à votre liste d’applications SaaS managées dans le portail Azure.

**Pour ajouter Mitel Connect à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Cliquez sur **Nouvelle application**.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Tapez **Mitel Connect** dans le champ de recherche, cliquez sur **Mitel Connect** dans le volet de résultats, puis sur **Ajouter**.

     ![Mitel Connect dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de MiCloud Connect avec un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur MiCloud Connect associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD auprès de MiCloud Connect, vous devez effectuer les étapes suivantes :

1. **[Configurer MiCloud Connect pour l’authentification unique avec Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité et pour configurer les paramètres d’authentification unique côté application.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
4. **[Créer un utilisateur de test Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** pour avoir un équivalent de Britta Simon dans votre compte MiCloud Connect lié à la représentation Azure AD de l’utilisateur.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurer MiCloud Connect pour l’authentification unique auprès d’Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD pour MiCloud Connect dans le portail Azure, et configurer votre compte MiCloud Connect pour autoriser l’authentification unique à l’aide d’Azure AD.

Pour configurer MiCloud Connect avec l’authentification unique pour Azure AD, il est plus facile d’ouvrir le portail Azure et le portail de compte Mitel côte à côte. Vous devrez copier certaines informations du portail Azure vers le portail de compte Mitel, et d’autres du portail de compte Mitel vers le portail Azure.


1. Pour ouvrir la page de configuration dans le [portail Azure](https://portal.azure.com/), effectuez les étapes suivantes :

    a. Dans la page d’intégration de l’application **Mitel Connect**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

    b. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **SAML**.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)
    
    La page d’authentification basée sur SAML s’affiche.

2. Pour ouvrir la boîte de dialogue de configuration dans le portail de compte Mitel, effectuez les étapes suivantes :

    a. Dans le menu **Phone System** (Système téléphonique), cliquez sur **Add-On Features** (Fonctionnalités supplémentaires).

    b. À droite de **Single Sign-On** (Authentification unique), cliquez sur **Activate** (Activer) ou **Settings** (Paramètres).
    
    La boîte de dialogue Connect Single Sign-On Settings (Paramètres d’authentification unique Connect) s’affiche.
    
3. Cochez la case **Enable Single Sign-On** (Activer l’authentification unique).
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Dans le portail Azure, cliquez sur l’icône **Modifier** dans la section **Configuration SAML de base**.
    ![image](common/edit-urls.png)

    La boîte de dialogue Configuration SAML de base s’affiche.

5.  Copiez l’URL du champ **Mitel Identifier (Entity ID)** dans le portail de compte Mitel et collez-la dans le champ **Identificateur (ID d’entité)** dans le portail Azure.

6. Copiez l’URL du champ **Reply URL (Assertion Consumer Service URL)** dans le portail de compte Mitel et collez-la dans le champ **URL de réponse (URL Assertion Consumer Service)** dans le portail Azure.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Dans la zone de texte **Sign on URL** (URL d’authentification), tapez l’une des URL suivantes :

    * **https://portal.shoretelsky.com** pour utiliser le portail de compte Mitel en tant qu’application Mitel par défaut.
    * **https://teamwork.shoretel.com** pour utiliser Teamwork en tant qu’application Mitel par défaut.

    **REMARQUE** : L’application Mitel par défaut est celle à laquelle un utilisateur accède quand il clique sur la vignette Mitel Connect dans le volet d’accès. Il s’agit également de l’application sollicitée lors d’une configuration de test à partir d’Azure AD.

8. Cliquez sur **Enregistrer** dans la boîte de dialogue **Configuration SAML de base** dans le portail Azure.

9. Dans la section **Certificat de signature SAML** de la page **Authentification basée sur SAML** du portail Azure, cliquez sur **Télécharger** en regard de **Certificat (Base64)** pour télécharger le **Certificat de signature** et l’enregistrer sur votre ordinateur.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Ouvrez le fichier de certificat de signature dans un éditeur de texte, copiez toutes les données contenues dans le fichier, puis collez-les dans le champ **Signing Certificate** (Certificat de signature) dans le portail de compte Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Dans la section **Configurer Mitel Connect** de la page **Authentification basée sur SAML** du portail Azure, effectuez les étapes suivantes :

    a. Copiez l’URL du champ **URL de connexion** et collez-la dans le champ **Sign-in URL** dans le portail de compte Mitel.

    b. Copiez l’URL du champ **Identificateur Azure AD** et collez-la dans le champ **Entity ID** dans le portail de compte Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Cliquez sur **Enregistrer** dans la boîte de dialogue **Connect Single Sign-On Settings** (Paramètres d’authentification unique Connect) dans le portail de compte Mitel.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, cliquez sur **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Cliquez sur **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans la boîte de dialogue de propriétés Utilisateur, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans la champ **Nom**, tapez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez brittasimon@\<domaine_de_votre_société\>.\<extension\>.  
Par exemple : BrittaSimon@contoso.com.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mitel Connect.

1. Dans le portail Azure, cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, cliquez sur **Mitel Connect**.

    ![Lien Mitel Connect dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur **Ajouter un utilisateur**, puis sur **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**, puis cliquez sur **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, sélectionnez le rôle approprié pour l’utilisateur dans la liste dans la boîte de dialogue **Sélectionner un rôle**, puis cliquez sur **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur **Attribuer**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Créer un utilisateur de test Mitel MiCloud Connect

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans votre compte MiCloud Connect. Les utilisateurs doivent être créés et activés avant l’utilisation de l’authentification unique.

Pour plus d’informations sur l’ajout d’utilisateurs dans le portail de compte Mitel, consultez l’article [Adding a User](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) (Ajout d’un utilisateur) dans la Base de connaissances Mitel.

Créez un utilisateur dans votre compte MiCloud Connect avec les détails suivants :

  * **Nom :** Britta Simon

* **Adresse e-mail professionnelle :** `brittasimon@<yourcompanydomain>.<extension>`   
(Par exemple [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nom d’utilisateur :** `brittasimon@<yourcompanydomain>.<extension>`  
(Par exemple [brittasimon@contoso.com](mailto:brittasimon@contoso.com). Le nom d’utilisateur est généralement identique à l’adresse e-mail professionnelle de l’utilisateur)

**REMARQUE :** Le nom d’utilisateur de l’utilisateur MiCloud Connect doit être identique à son adresse e-mail dans Azure.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Mitel Connect dans le volet d’accès, vous devez être redirigé automatiquement pour vous connecter à l’application MiCloud Connect que vous avez configurée comme application par défaut dans le champ **URL de connexion**. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
