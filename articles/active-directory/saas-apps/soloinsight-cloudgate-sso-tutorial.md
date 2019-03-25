---
title: 'Didacticiel : Intégration d’Azure Active Directory à Soloinsight-CloudGate SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847245"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Tutoriel : Intégration d’Azure Active Directory à Soloinsight-CloudGate SSO

Dans ce tutoriel, vous allez apprendre à intégrer Soloinsight-CloudGate SSO à Azure Active Directory (Azure AD).
L’intégration de Soloinsight-CloudGate SSO à Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à Soloinsight-CloudGate SSO.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Soloinsight-CloudGate SSO (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Soloinsight-CloudGate SSO, vous devez disposer des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Soloinsight-CloudGate SSO pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Soloinsight-CloudGate SSO prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Ajout de Soloinsight-CloudGate SSO à partir de la galerie

Pour configurer l’intégration de Soloinsight-CloudGate SSO à Azure AD, vous devez ajouter Soloinsight-CloudGate SSO à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Soloinsight-CloudGate SSO à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Soloinsight-CloudGate SSO**, sélectionnez **Soloinsight-CloudGate SSO** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Soloinsight-CloudGate SSO dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Soloinsight-CloudGate SSO grâce à un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Soloinsight-CloudGate SSO associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Soloinsight-CloudGate SSO, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** pour avoir un équivalent de Britta Simon dans Soloinsight-CloudGate SSO lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Soloinsight-CloudGate SSO, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Soloinsight-CloudGate SSO** et sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Soloinsight-CloudGate SSO](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.sigateway.com/login`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels, tel qu’expliqué dans la section **Configurer l’authentification unique Soloinsight-CloudGate SSO** de ce tutoriel.

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Soloinsight-CloudGate SSO**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Configurer l’authentification unique Soloinsight-CloudGate SSO

1. Pour obtenir les valeurs qui doivent être collées dans le portail Azure lors de la configuration SAML de base, connectez-vous au portail web CloudGate à l’aide de vos informations d’identification, puis accédez aux paramètres d’authentification unique à l’emplacement suivant : **Home>Administration>System settings>General**.

    ![Paramètres CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL de consommateur SAML**

    * Copiez les liens indiqués dans les champs **Saml Consumer URL** (URL de consommateur SAML) et **Redirect URL** (URL de redirection), et collez-les dans le portail Azure dans la section **Configuration SAML de base**, respectivement dans les champs **Identificateur (ID d’entité)** et **URL de réponse**.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Certificat de signature SAML**

    * Accédez à la source du fichier de certificat (Base64) téléchargé à partir des listes de certificats de signature SAML sur le portail Azure, et cliquez dessus avec le bouton droit. Choisissez l’option **Edit with Notepad++** (Modifier avec Notepad++) dans la liste. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copiez le contenu dans le fichier de certificat (Base64) Notepad++.

        ![Copie du certificat](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Collez le contenu dans le champ **Certificate** des paramètres d’authentification unique dans le portail web CloudGate et cliquez sur le bouton Save (Enregistrer).

        ![Portail de certificat](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Groupe par défaut**

    * Sélectionnez **Business Admin** (Administrateur d’entreprise) dans la liste déroulante de l’option **Default Group** (Groupe par défaut) dans le portail web CloudGate.

        ![Groupe par défaut](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Identificateur AD et URL de connexion**

    * L’**URL de connexion** copiée à partir des paramètres **Configurer l’authentification unique Soloinsight-CloudGate** dans le portail Azure doit être entrée dans la section de paramètres d’authentification unique du portail web CloudGate. 

    * Collez le lien **URL de connexion** du portail Azure dans le champ **AD Login URL** (URL de connexion AD) du portail web CloudGate.
     
    * Collez le lien **Identificateur Azure AD** du portail Azure dans le champ **AD Identifier** (Identificateur AD) du portail web CloudGate.

        ![Connexion AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Soloinsight-CloudGate SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Soloinsight-CloudGate SSO**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Soloinsight-CloudGate SSO**.

    ![Lien Soloinsight-CloudGate SSO dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Créer utilisateur de test Soloinsight-CloudGate SSO

Pour créer un utilisateur de test, sélectionnez **Employees** dans le menu principal du portail web CloudGate et renseignez le formulaire Add New employee (Ajouter un nouvel employé). Le niveau d’autorité qui doit être affecté à l’utilisateur de test est **Business Admin** (Administrateur d’entreprise). Cliquez sur **Create** (Créer) une fois que vous avez renseigné tous les champs obligatoires.

![Test de l’employé](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Soloinsight-CloudGate SSO dans le volet d’accès, vous devez être connecté automatiquement à l’application Soloinsight-CloudGate SSO pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

