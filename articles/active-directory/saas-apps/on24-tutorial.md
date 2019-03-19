---
title: 'Tutoriel : Intégration d’Azure Active Directory à ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: f95420397a502af8e939c8a53fe5635b82dc0f39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893643"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Didacticiel : Intégration d’Azure Active Directory à ON24 Virtual Environment SAML Connection

Dans ce didacticiel, vous allez apprendre à intégrer ON24 Virtual Environment SAML Connection à Azure Active Directory (Azure AD).
ON24 Virtual Environment SAML Connection dans Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à ON24 Virtual Environment SAML Connection.
* Vous pouvez autoriser vos utilisateurs à être automatiquement connecté à ON24 connexion SAML environnement virtuel (Single Sign-On) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à ON24 Virtual Environment SAML Connection, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Connexion SAML environnement virtuel de ON24 l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Prend en charge la connexion SAML environnement virtuel de ON24 **SP** et **IDP** initiée par l’authentification unique

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Ajout de la connexion SAML à l’environnement virtuel ON24 depuis la galerie

Pour configurer l’intégration de la connexion SAML à l’environnement virtuel ON24 à Azure AD, vous devez ajouter la connexion SAML à l’environnement virtuel ON24 à votre liste d’applications SaaS managées depuis la galerie.

**Pour ajouter la connexion SAML à l’environnement virtuel ON24 depuis la galerie, procédez aux étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **connexion SAML à l’environnement virtuel ON24**, sélectionnez **connexion SAML à l’environnement virtuel ON24** dans le volet de résultats puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![ON24 connexion SAML environnement virtuel dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez Azure AD-authentification unique avec ON24 environnement SAML connexion virtuelle basée sur un utilisateur de test appelé **Britta Simon**.
Pour l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur ON24 virtuel environnement SAML connexion doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec ON24 Virtual Environment SAML Connection, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer ON24 virtuel environnement SAML connexion Single Sign-On](#configure-on24-virtual-environment-saml-connection-single-sign-on)**  : pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer utilisateur de test de connexion SAML environnement virtuel de ON24](#create-on24-virtual-environment-saml-connection-test-user)**  - pour avoir un équivalent de Britta Simon dans ON24 virtuel environnement SAML de connexion qui est lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer Azure AD-authentification unique avec ON24 virtuel environnement SAML connexion, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ON24 Virtual Environment SAML Connection**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Domaine de connexion ON24 virtuel environnement SAML et URL unique des informations d’authentification](common/idp-relay.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL :

     **URL d’environnement de production**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL d’environnement assurance qualité**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL :

     **URL d’environnement de production**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL d’environnement assurance qualité**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Cliquez sur **Définir des URL supplémentaires**. 

    d. Dans la zone de texte **État de relais**, tapez une URL : `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    ![Domaine de connexion ON24 virtuel environnement SAML et URL unique des informations d’authentification](common/both-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://vshow.on24.com/vshow/<INSTANCENAME>`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettre à jour ces valeurs avec l’URL réelle de l’état de relais et l’authentification. Contact [équipe de support technique de connexion ON24 virtuel environnement SAML](https://www.on24.com/contact-us/) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Sur le **configurer ON24 virtuel environnement SAML connexion** section, copiez l’URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configurer ON24 environnement virtuel SAML connexion Single Sign-On

Pour configurer l’authentification unique sur **ON24 virtuel environnement SAML connexion** , vous devez envoyer le **XML des métadonnées de fédération** et appropriée copié l’URL à partir du portail Azure pour [ Équipe de support technique de connexion SAML environnement virtuel de ON24](https://www.on24.com/about-us/support/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le **nom d’utilisateur** type de champ **brittasimon\@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ON24 Virtual Environment SAML Connection.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **ON24 virtuel environnement SAML connexion**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **ON24 Virtual Environment SAML Connection**.

    ![Le lien de connexion SAML environnement virtuel de ON24 dans la liste des Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Créer utilisateur de test de connexion SAML environnement virtuel de ON24

Dans cette section, vous créez un utilisateur appelé Britta Simon dans ON24 Virtual Environment SAML Connection. Travaillez avec l’ [équipe du support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) pour ajouter des utilisateurs à la plateforme ON24 Virtual Environment SAML Connection. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette de connexion SAML environnement virtuel de ON24 dans le volet d’accès, vous devez être automatiquement connecté à la connexion de SAML d’environnement virtuel ON24 pour lequel vous configurez l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

