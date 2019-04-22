---
title: 'Didacticiel : Intégration d’Azure Active Directory à NetSuite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab329574ef425e8133ac746c185050efcc8bc15a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261443"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Didacticiel : Intégration d’Azure Active Directory à NetSuite

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à NetSuite.
L’intégration de NetSuite à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à NetSuite.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à NetSuite (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à NetSuite, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement NetSuite pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* NetSuite prend en charge l’authentification unique initiée par **IDP**
* NetSuite prend en charge l’attribution d’utilisateurs **Juste-à-temps**
* NetSuite prend en charge [l’approvisionnement automatique d’utilisateurs](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Ajout de NetSuite à partir de la galerie

Pour configurer l’intégration de NetSuite à Azure AD, vous devez ajouter NetSuite à votre liste d’applications SaaS managées, à partir de la galerie.

**Pour ajouter NetSuite à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **NetSuite**, sélectionnez **NetSuite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![NetSuite dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec NetSuite sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur NetSuite associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec NetSuite, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique NetSuite](#configure-netsuite-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test NetSuite](#create-netsuite-test-user)** pour avoir un équivalent de Britta Simon dans NetSuite qui soit lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec NetSuite, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **NetSuite**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL NetSuite](common/idp-reply.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique du client NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application NetSuite attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom | Attribut source | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

    >[!NOTE]
    >La valeur de l’attribut account n’est pas réelle. Vous devrez la remplacer. La procédure est expliquée plus loin dans le tutoriel.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer NetSuite**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-netsuite-single-sign-on"></a>Configurer l’authentification unique NetSuite

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise NetSuite en tant qu’administrateur.

2. Dans la barre d’outils en haut de la page, cliquez sur **Setup** (Configuration), puis accédez à **Company** (Entreprise) et cliquez sur **Enable Features** (Activer des fonctionnalités).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Dans la barre d’outils située au milieu de la page, cliquez sur **SuiteCloud**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Dans la section **Manage Authentication** (Gérer l’authentification), sélectionnez **SAML SINGLE SIGN-ON** (Authentification unique SAML) pour activer l’option du même nom dans NetSuite.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Dans la barre d’outils située en haut de la page, cliquez sur **Setup** (Configuration).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

6. Dans la liste **SETUP TASKS** (Tâches de configuration), sélectionnez **Integration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-integration.png)

7. Dans la section **MANAGE AUTHENTICATION** (Gérer l’authentification), cliquez sur **SAML Single Sign-on** (Authentification unique SAML).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-saml.png)

8. Dans la section **SAML Setup** (Configuration SAML) située sous **NetSuite Configuration** (Configuration NetSuite), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Sélectionnez **PRIMARY AUTHENTICATION METHOD** (Méthode d’authentification principale).

    b. Pour le champ intitulé **SAMLV2 IDENTITY PROVIDER METADATA** (Métadonnées du fournisseur d’identité SAMLV2), sélectionnez **UPLOAD IDP METADATA FILE** (Charger le fichier de métadonnées du fournisseur d’identité). Cliquez ensuite sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Cliquez sur **Envoyer**.

9. Dans NetSuite, cliquez sur **Setup** (Configuration), puis accédez à **Company** (Entreprise) et cliquez sur **Company Information** (Informations sur l’entreprise) dans le menu de navigation supérieur.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Dans la page **Company Information** (Informations sur l’entreprise), dans la colonne de droite, copiez la valeur du champ **ACCOUNT ID** (ID de compte).

    c. Collez **l’ID de compte** du compte NetSuite dans le champ **Valeur d’attribut** d’Azure AD. 

10. Avant que les utilisateurs puissent utiliser l’authentification unique dans NetSuite, vous devez d’abord leur affecter les autorisations appropriées dans NetSuite. Procédez comme suit pour attribuer ces instructions.

    a. Dans le menu de navigation supérieur, cliquez sur **Setup** (Configuration).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

    b. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les rôles**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Cliquez sur **Nouveau rôle**.

    d. Entrez un **nom** pour votre nouveau projet.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-new-role.png)

    e. Cliquez sur **Enregistrer**.

    f. Dans le menu situé en haut, cliquez sur **Autorisations**. Cliquez sur **Configuration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-sso.png)

    g. Sélectionnez **SAML Single Sign-on** (Authentification unique SAML), puis cliquez sur **Add** (Ajouter).

    h. Cliquez sur **Enregistrer**.

    i. Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-setup.png)

    j. Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les utilisateurs**.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Sélectionnez un utilisateur de test. Ensuite, cliquez sur **Edit** (Modifier), puis accédez à l’onglet **Access** (Accès).

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Dans la boîte de dialogue Roles (Rôles), attribuez le rôle que vous avez créé.

    ![Configurer l'authentification unique](./media/NetSuite-tutorial/ns-add-role.png)

    m. Cliquez sur **Enregistrer**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à NetSuite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **NetSuite**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **NetSuite**.

    ![Lien NetSuite dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-netsuite-test-user"></a>Créer un utilisateur de test NetSuite

Dans cette section, un utilisateur nommé Britta Simon est créé dans NetSuite. NetSuite prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans NetSuite, il en est créé un après l’authentification.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette NetSuite dans le panneau d’accès doit vous connecter automatiquement à l’application NetSuite pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](NetSuite-provisioning-tutorial.md)

