---
title: "Didacticiel : Intégration d'Azure Active Directory à iLMS | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bcb465f76e09675333e6e608249cba11f722e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274653"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Didacticiel : Intégration d'Azure Active Directory à iLMS

Dans ce didacticiel, vous allez apprendre à intégrer iLMS dans Azure Active Directory (Azure AD).
L’intégration d’iLMS dans Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à iLMS.
* Vous pouvez permettre à vos utilisateurs d’être automatiquement connectés à iLMS (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec iLMS, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement iLMS pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* iLMS prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="adding-ilms-from-the-gallery"></a>Ajout d’iLMS à partir de la galerie

Pour configurer l’intégration d’iLMS avec Azure AD, vous devez ajouter iLMS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter iLMS à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **iLMS**, sélectionnez **iLMS** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![iLMS dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’iLMS, à l’aide d’un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur iLMS associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec iLMS, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique d’iLMS](#configure-ilms-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer l’utilisateur de test iLMS](#create-ilms-test-user)** pour avoir dans iLMS un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès d’iLMS, effectuez les étapes suivantes :

1. Dans la page d’intégration de l’application **iLMS** sur le [portail Azure](https://portal.azure.com/), cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique relatives au domaine et aux URL iLMS](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, collez la valeur de **Identifier** (Identificateur) que vous copiez à partir de la section **Service Provider** (Fournisseur de services) des paramètres SAML dans le portail d’administration iLMS.

    b. Dans la zone de texte **URL de réponse**, collez la valeur de **Endpoint (URL)** (Point de terminaison (URL)) que vous copiez à partir de la section **Service Provider** (Fournisseur de services) des paramètres SAML dans le portail d’administration iLMS, avec le modèle suivant `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique relatives au domaine et aux URL iLMS](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, collez la valeur de **Endpoint (URL)** (Point de terminaison (URL)) que vous copiez à partir de la section **Service Provider** (Fournisseur de services) des paramètres SAML dans le portail d’administration iLMS en tant que `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Pour permettre le provisionnement JIT, votre application iLMS attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ouvrir la boîte de dialogue Attributs d’utilisateur.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Vous devez activer **Créer un compte utilisateur non reconnu** dans iLMS pour mapper ces attributs. Suivez les instructions [ici](http://support.inspiredelearning.com/customer/portal/articles/2204526) pour avoir une idée sur la configuration des attributs.

7. En plus de ce qui précède, l’application iLMS s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Nom | Attribut source|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer iLMS**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-ilms-single-sign-on"></a>Configurer l’authentification unique d’iLMS

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration iLMS** en tant qu’administrateur.

2. Cliquez sur **SSO:SAML** sous l’onglet **Paramètres** pour ouvrir les paramètres SAML et effectuer les opérations suivantes :

    ![Configurer l'authentification unique](./media/ilms-tutorial/1.png)

3. Développez la section **Fournisseur de services** et copiez les valeurs **Identificateur** et **URL du point de terminaison**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/2.png) 

4. Sous la section **Fournisseur d’identité**, cliquez sur **importer les métadonnées**.

5. Sélectionnez le fichier **Métadonnées de fédération** téléchargé depuis le portail Azure, à partir de la section **Certificat de signature SAML**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Si vous souhaitez activer JIT pour approvisionner des comptes iLMS pour l’annulation de reconnaissance d’utilisateurs, procédez comme suit :

    a. Cochez **Créer un compte utilisateur non reconnu**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mappez les attributs dans Azure AD avec les attributs dans iLMS. Dans la colonne d’attribut, spécifiez le nom des attributs ou la valeur par défaut.

    c. Accédez à l’onglet **Règles d’entreprise** et effectuez les opérations suivantes :

    ![Configurer l'authentification unique](./media/ilms-tutorial/5.png)

    d. Cochez **Créer des régions, divisions et départements non reconnus** pour créer des régions, divisions et départements qui n’existent pas encore au moment de l’authentification unique.

    e. Cochez **Mettre à jour le profil utilisateur lors de la connexion** pour spécifier si le profil utilisateur est mis à jour à chaque ouverture de session unique.

    f. Si l’option **Mettre à jour les valeurs vides pour les champs non obligatoires dans le profil utilisateur** est cochée, les champs facultatifs qui sont vides lors de la connexion provoquent également la présence de valeurs vides pour ces champs dans le profil iLMS de l’utilisateur.

    g. Cochez **Envoyer un e-mail de notification d’erreur** et entrez l’adresse e-mail de l’utilisateur pour lequel vous souhaitez recevoir l’e-mail de notification d’erreur.

7. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

    ![Configurer l'authentification unique](./media/ilms-tutorial/save.png)

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à iLMS.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **iLMS**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **iLMS**.

    ![Lien iLMS dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-ilms-test-user"></a>Créer l’utilisateur de test iLMS

L’application prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. JIT fonctionnera si vous avez coché la case **Créer un compte utilisateur non reconnu** lors de la configuration de SAML sur le portail d’administration iLMS.

Si vous avez besoin de créer un utilisateur manuellement, suivez les étapes ci-dessous :

1. Connectez-vous à votre site d’entreprise iLMS en tant qu’administrateur.

2. Cliquez sur **Register User** (Inscrire un utilisateur) sous l’onglet **Users** (Utilisateurs) pour ouvrir la page **Register User** (Inscrire un utilisateur).

   ![Ajouter un employé](./media/ilms-tutorial/3.png)

3. Dans la page **Register User** (Inscrire un utilisateur), effectuez les étapes suivantes.

    ![Ajouter un employé](./media/ilms-tutorial/create_testuser_add.png)

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom, par exemple Britta.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom, par exemple Simon.

    c. Dans la zone de texte **Email ID** (Identificateur e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple BrittaSimon@contoso.com.

    d. Dans la liste déroulante **Région**, sélectionnez la valeur pour la région.

    e. Dans la liste déroulante **Division**, sélectionnez la valeur pour la division.

    f. Dans la liste déroulante **Département**, sélectionnez une valeur pour le département.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Vous pouvez envoyer un courrier d’inscription à l’utilisateur en cochant la case **Send Registration Mail** (Envoyer un e-mail d’inscription).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette iLMS dans le volet d’accès doit vous connecter automatiquement à l’application iLMS pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)