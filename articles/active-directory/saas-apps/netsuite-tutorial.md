---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à NetSuite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à NetSuite

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à NetSuite. Quand vous intégrez NetSuite à Azure AD, vous pouvez :

* Contrôler qui a accès à NetSuite dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à NetSuite avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement NetSuite pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* NetSuite prend en charge l’authentification unique initiée par **IDP**

* NetSuite prend en charge l’attribution d’utilisateurs **Juste-à-temps**

* NetSuite prend en charge [l’approvisionnement automatique d’utilisateurs](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-netsuite-from-the-gallery"></a>Ajout de NetSuite à partir de la galerie

Pour configurer l’intégration de NetSuite à Azure AD, vous devez ajouter NetSuite à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **NetSuite** dans la zone de recherche.
1. Sélectionnez **NetSuite** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurer et tester l’authentification unique Azure AD pour NetSuite

Configurez et testez l’authentification unique Azure AD avec NetSuite à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur NetSuite associé.

Pour configurer et tester l’authentification unique Azure AD avec NetSuite, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique NetSuite](#configure-netsuite-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test NetSuite](#create-netsuite-test-user)** pour avoir un équivalent de B.Simon dans NetSuite qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **NetSuite**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant :

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique du client NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application NetSuite s’attend à recevoir les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue Attributs d’utilisateur.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application NetSuite s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section Revendications des utilisateurs de la boîte de dialogue Attributs utilisateur, effectuez les étapes suivantes pour ajouter le jeton SAML, comme indiqué dans le tableau ci-dessous :

    | Nom | Attribut source | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    1. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    1. Laissez le champ **Espace de noms** vide.

    1. Sélectionnez Source comme **Attribut**.

    1. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    1. Cliquez sur **OK**.

    1. Cliquez sur **Enregistrer**.

    >[!NOTE]
    >La valeur de l’attribut account n’est pas réelle. Vous devrez la remplacer. La procédure est expliquée plus loin dans le tutoriel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer NetSuite**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à NetSuite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **NetSuite**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-netsuite-sso"></a>Configurer l’authentification unique NetSuite

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

### <a name="create-netsuite-test-user"></a>Créer un utilisateur de test NetSuite

Dans cette section, un utilisateur nommé Britta Simon est créé dans NetSuite. NetSuite prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans NetSuite, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette NetSuite dans le panneau d’accès doit vous connecter automatiquement à l’application NetSuite pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer NetSuite avec Azure AD](https://aad.portal.azure.com/)

