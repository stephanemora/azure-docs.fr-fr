---
title: 'Tutoriel : Intégration d’Azure Active Directory à Comm100 Live Chat | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 1e0fbf7678ddac76d1a31c4bde4d75545b429add
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700690"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Tutoriel : Intégration d’Azure Active Directory à Comm100 Live Chat

Dans ce didacticiel, vous allez apprendre à intégrer Comm100 Live Chat à Azure Active Directory (Azure AD).
L’intégration de Comm100 Live Chat à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Comm100 Live Chat.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Comm100 Live Chat (au moyen de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Comm100 Live Chat, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Comm100 Live Chat pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Comm100 Live Chat prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Ajout de Comm100 Live Chat à partir de la galerie

Pour configurer l’intégration de Comm100 Live Chat avec Azure AD, vous devez ajouter Comm100 Live Chat à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Comm100 Live Chat à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Comm100 Live Chat**, sélectionnez **Comm100 Live Chat** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Comm100 Live Chat dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Comm100 Live Chat avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Comm100 Live Chat associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Comm100 Live Chat, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Comm100 Live Chat](#configure-comm100-live-chat-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Comm100 Live Chat](#create-comm100-live-chat-test-user)** pour avoir un équivalent de Britta Simon dans Comm100 Live Chat lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Comm100 Live Chat, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Comm100 Live Chat**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Comm100 Live Chat Domain and URLs (Domaine et URL Comm100 Live Chat)](common/sp-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Vous mettrez à jour cette valeur avec l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel.

5. L’application Comm100 Live Chat attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit : 

    | Nom |  Attribut source|
    | ---------------| --------------- |
    |   email    | user.mail |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Comm100 Live Chat**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-comm100-live-chat-single-sign-on"></a>Configurer l’authentification unique Comm100 Live Chat

9. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Comm100 Live Chat en tant qu’administrateur de la sécurité.

10. Dans l’angle supérieur droit de la page, cliquez sur **My Account** (Mon compte).

    ![Comm100 Live Chat : myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. Dans le menu, à gauche, cliquez sur **Security** (Sécurité), puis sur **Agent Single Sign-On** (Authentification unique des agents).

    ![Comm100 Live Chat : sécurité](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Dans la page **Agent Single Sign-On** (Authentification unique des agents), effectuez les opérations suivantes :

    ![Comm100 Live Chat : sécurité](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copiez le premier lien en surbrillance et collez-le dans la zone de texte **URL de connexion** de la section **Comm100 Live Chat Domain and URLs** (Domaine et URL Comm100 Live Chat) du Portail Azure.

    b. Dans la zone de texte **SAML SSO URL** (URL d’authentification unique SAML), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Remote Logout URL** (URL de déconnexion à distance), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du Portail Azure.

    d. Sous **Certificat**, cliquez sur **Choose a File** (Choisir un fichier) pour charger le certificat codé en base 64 que vous avez téléchargé à partir du Portail Azure.

    e. Cliquez sur **Enregistrer les modifications**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Comm100 Live Chat.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Comm100 Live Chat**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Comm100 Live Chat**.

    ![Lien Comm100 Live Chat dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-comm100-live-chat-test-user"></a>Créer un utilisateur de test Comm100 Live Chat

Pour permettre aux utilisateurs d’Azure AD de se connecter à Comm100 Live Chat, vous devez les approvisionner dans Comm100 Live Chat. Dans Comm100 Live Chat, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Comm100 Live Chat en tant qu’administrateur de la sécurité.

2. Dans l’angle supérieur droit de la page, cliquez sur **My Account** (Mon compte).

    ![Comm100 Live Chat : myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Dans le menu, à gauche, cliquez sur **Agents**, puis sur **New Agent** (Nouvel agent).

    ![Comm100 Live Chat : agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Dans la page **New Agent** (Nouvel agent), effectuez les opérations suivantes :

    ![Comm100 Live Chat : nouvel agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Last Name** (Nom), saisissez le nom de famille de l’utilisateur, par exemple **Simon**.

    d. Dans la zone de texte **Display Name** (Nom d’affichage), tapez le nom d’affichage de l’utilisateur, par exemple, **Britta Simon**.

    e. Dans la zone de texte **Password** (Mot de passe), tapez votre mot de passe.

    f. Cliquez sur **Enregistrer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Comm100 Live Chat dans le volet d’accès, vous devez être connecté automatiquement à l’application Comm100 Live Chat pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

