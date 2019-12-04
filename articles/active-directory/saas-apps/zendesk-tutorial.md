---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zendesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233267"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Didacticiel : Intégration d’Azure Active Directory à Zendesk

Dans ce didacticiel, vous allez apprendre à intégrer Zendesk à Azure Active Directory (Azure AD).
L’intégration de Zendesk à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Zendesk.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Zendesk (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zendesk, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Zendesk pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zendesk prend en charge l’authentification unique initiée par le **fournisseur de services**

* Zendesk prend en charge [l’attribution d’utilisateurs **automatique**](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Ajout de Zendesk depuis la galerie

Pour configurer l’intégration de Zendesk à Azure AD, vous devez ajouter Zendesk, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zendesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Zendesk**, sélectionnez **Zendesk** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Zendesk dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zendesk, sur un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Zendesk associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Zendesk, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Zendesk](#configure-zendesk-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Zendesk](#create-zendesk-test-user)**  : pour avoir un équivalent de Britta Simon dans Zendesk lié à la représentation de l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Zendesk, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Zendesk**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Zendesk](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.zendesk.com`

   b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Zendesk attend les assertions SAML dans un format spécifique. Il n’y a aucun attribut SAML obligatoire, mais si vous le souhaitez, vous pouvez les gérer à partir de la section **Attributs utilisateur** dans la page d’intégration de l’application. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, sélectionnez la valeur d’attribut appropriée.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Utilisez des attributs d’extension pour ajouter des attributs qui ne sont pas dans Azure AD par défaut. Cliquez sur [les attributs d’utilisateur qui peuvent être définis dans SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) pour obtenir la liste complète des attributs SAML que **Zendesk** accepte.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, copiez l’**empreinte**, puis enregistrez-la sur votre ordinateur.

    ![Copier la valeur de l’empreinte](common/copy-certificatethumbprint.png)

8. Dans la section **Configurer Zendesk**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

9. Il existe deux modes de configuration de Zendesk : Automatique et Manuel.
  
10. Pour automatiser la configuration dans Zendesk, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Zendesk**, vous êtes alors orienté vers l’application Zendesk. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Zendesk. Cette extension de navigateur configure automatiquement l’application pour vous et automatise la section **Configurer l’authentification unique Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configurer l’authentification unique Zendesk

1. Si vous souhaitez configurer manuellement Zendesk, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zendesk en tant qu’administrateur et procédez comme suit :

2. Cliquez sur **Admin**.

3. Dans le volet de navigation gauche, cliquez sur **Settings**, puis sur **Security**.

4. Sur la page **Sécurité**, procédez comme suit :

    ![Sécurité](././media/zendesk-tutorial/ic773089.png "Sécurité")

    ![Authentification unique](././media/zendesk-tutorial/ic773090.png "Authentification unique")

    a. Cliquez sur l’onglet **Administrateurs et Agents**.

    b. Sélectionnez **Single sign-on (SSO) and SAML**, puis **SAML**.

    c. Dans la zone de texte **SAML SSO URL** (URL SSO SAML), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **Remote Logout URL** (URL de déconnexion distante), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Empreinte du certificat**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zendesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Zendesk**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Zendesk**.

    ![Lien Zendesk dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-zendesk-test-user"></a>Créer un utilisateur de test Zendesk

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Zendesk. Zendesk prend en charge l’approvisionnement automatique d’utilisateurs, qui est activé par défaut. Des informations supplémentaires sur la configuration de l’approvisionnement automatique d’utilisateurs sont disponibles [ici](Zendesk-provisioning-tutorial.md).

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

> [!NOTE]
> Les comptes de **l’utilisateur final** sont automatiquement approvisionnés lors de la connexion. Les comptes **Agent** et **Admin** doivent être approvisionnés manuellement dans **Zendesk** avant de vous connecter.

1. Connectez-vous à votre client **Zendesk** .

2. Sélectionnez l’onglet **Customer List** .

3. Sélectionnez l’onglet **User**, puis cliquez sur **Add**.

    ![Ajouter un utilisateur](././media/zendesk-tutorial/ic773632.png "Ajouter un utilisateur")
4. Tapez le **Nom** et **l’e-mail** d’un compte Azure AD que vous souhaitez approvisionner, puis cliquez sur **Save**.

    ![Nouvel utilisateur](././media/zendesk-tutorial/ic773633.png "Nouvel utilisateur")

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zendesk pour provisionner des comptes d’utilisateurs Azure AD.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Zendesk dans le panneau d’accès doit vous connecter automatiquement à l’application Zendesk pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](zendesk-provisioning-tutorial.md)