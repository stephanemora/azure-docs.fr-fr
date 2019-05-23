---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Cisco Webex Meetings | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 308f745489fba2e2b539a2f2615b65228565dcf9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900088"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Didacticiel : Intégration d’Azure Active Directory avec Cisco Webex Meetings

Ce didacticiel explique comment intégrer Intégration d’Azure Active Directory avec Cisco Webex Meetings avec Azure Active Directory (Azure AD).
L’intégration de Intégration d’Azure Active Directory avec Cisco Webex Meetings avec Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Cisco Webex Meetings.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Cisco Webex Meetings (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Cisco Webex Meetings, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Cisco Webex Meetings pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cisco Webex Meetings prend en charge l’authentification unique initiée par le **fournisseur de services**

* Cisco Webex Meetings prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Ajout de Cisco Webex Meetings à partir de la galerie

Pour configurer l’intégration de Cisco Webex Meetings à Azure AD, vous devez ajouter Cisco Webex Meetings, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco Webex Meetings à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Cisco Webex Meetings**, sélectionnez **Cisco Webex Meetings** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Cisco Webex Meetings dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cisco Webex Meetings sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Cisco Webex Meetings associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Cisco Webex Meetings, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Cisco Webex Meetings](#configure-cisco-webex-meetings-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** pour avoir un équivalent de Britta Simon dans Cisco Webex Meetings, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Cisco Webex Meetings, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Cisco Webex Meetings**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans le portail Azure, dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, chargez le **fichier de métadonnées du fournisseur de services** téléchargé et effectuez les étapes suivantes pour configurer l’application :

    >[!Note]
    >Vous recevez le fichier de métadonnées de fournisseur de services, décrit plus loin dans la section **Configurer l’authentification unique Cisco Webex Meetings** du didacticiel. 

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le chargement du fichier de métadonnées du fournisseur de services terminé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement remplies dans la section **Configuration SAML de base**, comme indiqué ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex Meetings](common/sp-identifier-reply.png)

    Dans la zone de texte **URL de connexion**, collez la valeur d’**URL de réponse** renseignée automatiquement par chargement du fichier de métadonnées du fournisseur de services.

5. L’application Cisco Webex Meetings s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ajouter les attributs.

    ![image](common/edit-attribute.png)

6. Supprimez les attributs par défaut de la section **Revendications des utilisateurs**. L’application Cisco Webex Meetings s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :
    
    | Nom | Attribut source|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Cisco Webex Meetings**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Configurer l’authentification unique Cisco Webex Meetings

1. Accédez à [Cisco Cloud Collaboration Management](https://www.webex.com/go/connectadmin) (Gestion de la collaboration dans le cloud Cisco) avec vos informations d’identification d’administrateur.

2. Accédez à **Paramètres de sécurité**, puis à **Federated Web SSO Configuration** (Configuration de SSO de Web fédéré).
 
    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Dans la section **Federated Web SSO Configuration** (Configuration de SSO de Web fédéré), procédez comme suit :

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Dans la zone de texte Federation Protocol (Protocole de fédération), tapez le nom de votre protocole.

    b. Cliquez sur le lien **Import SAML Metadata** (Importer les métadonnées SAML) pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Cliquez sur le bouton **Exporter** pour télécharger le fichier de métadonnées du fournisseur de services, puis chargez-le dans la section **Configuration SAML de base** sur le portail Azure.

    d. Dans la zone de texte **AuthContextClassRef**, tapez `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` et, si vous souhaitez activer l’authentification multifacteur à l’aide d’Azure AD, tapez les deux valeurs telles que `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Sélectionnez **Auto Account Creation** (Création de compte automatique).

    >[!NOTE]
    >Pour activer l’approvisionnement d’utilisateurs **juste-à-temps**, vous devez activer l’option **Auto Account Creation** (Création du compte automatique). En plus de cela, les attributs du jeton SAML doivent être transmis dans la réponse SAML.

    f. Cliquez sur **Enregistrer**. 

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Webex Meetings.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Cisco Webex Meetings**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cisco Webex Meetings**.

    ![Lien Cisco Webex Meetings dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-cisco-webex-meetings-test-user"></a>Créer un utilisateur de test Cisco Webex Meetings

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Cisco Webex Meetings. Cisco Webex Meetings prend en charge l’approvisionnement **juste-à-temps**, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Cisco Webex Meetings, il en est créé un quand vous tentez d’y accéder.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Cisco Webex Meetings dans le panneau d’accès doit vous connecter automatiquement à l’application Cisco Webex Meetings pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

