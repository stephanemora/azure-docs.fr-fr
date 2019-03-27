---
title: 'Tutoriel : Intégration d’Azure Active Directory à Cisco Webex | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb64495d727429ff4dcfc233adee7dd5c60aaa8d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852132"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Didacticiel : Intégration d’Azure Active Directory à Cisco Webex

Ce didacticiel explique comment intégrer Cisco Webex avec Azure Active Directory (Azure AD).
L’intégration de Cisco Webex avec Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Cisco Webex.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Cisco Webex (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Cisco Webex, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Cisco Webex pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Cisco Webex prend en charge l’authentification unique initiée par le **fournisseur de services**

* Cisco Webex prend en charge l’attribution d’utilisateurs **automatique**.

## <a name="adding-cisco-webex-from-the-gallery"></a>Ajout de Cisco Webex à partir de la galerie

Pour configurer l’intégration de Cisco Webex à Azure AD, vous devez ajouter Cisco Webex, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco Webex à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Cisco Webex**, sélectionnez **Cisco Webex** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Cisco Webex dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cisco Webex sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Cisco Webex associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Cisco Webex, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Cisco Webex](#configure-cisco-webex-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Cisco Webex](#create-cisco-webex-test-user)** pour avoir un équivalent de Britta Simon dans Cisco Webex, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Cisco Webex, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Cisco Webex**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL comme `https://web.ciscospark.com/#/signin`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://idbroker.webex.com/<Org Id>`

    > [!NOTE]
    > Cette valeur d’identificateur n’est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Si vous avez des métadonnées de fournisseur de services, chargez-les dans la section **Configuration SAML de base**. La valeur **Identificateur (ID d’entité)** est renseignée automatiquement.

5. L’application Cisco Webex s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ajouter les attributs.

    ![image](common/edit-attribute.png)

6. En plus de ce qui précède, l’application Cisco Webex s’attend à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :
    
    | Nom |  Attribut source|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Cisco Webex**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-cisco-webex-single-sign-on"></a>Configurer l’authentification unique Cisco Webex

1. Connectez-vous à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Gestion de la collaboration dans le cloud Cisco) avec vos informations d’identification d’administrateur complètes.

2. Sélectionnez **Settings** (Paramètres), puis, dans la section **Authentication** (Authentification), cliquez sur **Modify** (Modifier).

    ![Configurer l'authentification unique](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Sélectionnez **intégrer un fournisseur d’identité tiers. (Avancé)** et accédez à l’écran suivant.

4. Dans la page **Import Idp Metadata** -(Importer les métadonnées Idp), glissez-déposez le fichier de métadonnées Azure AD sur la page, ou utilisez l’option d’explorateur de fichiers pour localiser et charger le fichier de métadonnées Azure AD. Ensuite, sélectionnez **Require certificate signed by a certificate authority in Metadata (more secure)** (Exiger un certificat signé par une autorité de certification dans les métadonnées (plus sûr)), puis cliquez sur **Next** (Suivant).

    ![Configurer l'authentification unique](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Sélectionnez **Test SSO Connection** (Tester la connexion SSO), puis, quand un nouvel onglet de navigateur s’ouvre, et authentifiez-vous auprès d’Azure AD en vous connectant.

6. Revenez à l’onglet du navigateur **Cisco Cloud Collaboration Management** (Gestion de la collaboration dans le cloud Cisco). Si le test a réussi, sélectionnez **Ce test a réussi. option Activer l’authentification unique** et cliquez sur **Suivant**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@yourcompanydomain.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Webex.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Cisco Webex**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cisco Webex**.

    ![Lien Cisco Webex dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-cisco-webex-test-user"></a>Créer un utilisateur de test Cisco Webex

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Cisco Webex. Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Cisco Webex.

1. Accédez à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Gestion de la collaboration dans le cloud Cisco) avec vos informations d’identification d’administrateur complètes.

2. Cliquez sur **Utilisateurs**, puis sur **Gérer les utilisateurs**.
   
    ![Configurer l'authentification unique](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Dans la fenêtre **Manage User** (Gérer l’utilisateur), sélectionnez **Manually add or modify users** (Ajouter ou modifier manuellement des utilisateurs), puis cliquez sur **Next** (Suivant).

4. Sélectionnez **Names and Email address** (Noms et adresse de messagerie). Ensuite, complétez la zone de texte comme suit :

    ![Configurer l'authentification unique](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom de famille), tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple **britta.simon\@contoso.com**.

5. Cliquez sur le signe plus pour ajouter Britta Simon. Cliquez ensuite sur **Suivant**.

6. Dans la fenêtre **Add Services for Users** (Ajouter des services pour les utilisateurs), cliquez sur **Save** (Enregistrer), puis sur **Finish** (Terminer).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Cisco Webex dans le panneau d’accès doit vous connecter automatiquement à l’application Cisco Webex pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial) 
