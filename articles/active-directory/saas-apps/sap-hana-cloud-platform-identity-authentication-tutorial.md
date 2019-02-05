---
title: "Tutoriel : Intégration d'Azure Active Directory à SAP Cloud Platform Identity Authentication | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 6b5b664581a1f3da367f74318cfb6bf5564e5b39
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472879"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutoriel : Intégration d'Azure Active Directory à SAP Cloud Platform Identity Authentication

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud Platform Identity Authentication à Azure Active Directory (Azure AD).
L’intégration de SAP Cloud Platform Identity Authentication avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SAP Cloud Platform Identity Authentication.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à SAP Cloud Platform Identity Authentication (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SAP Cloud Platform Identity Authentication, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement SAP Cloud Platform Identity Authentication pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SAP Cloud Platform Identity Authentication prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**

Avant de rentrer dans les détails techniques, il est essentiel de comprendre les concepts que vous allez voir. SAP Cloud Platform Identity Authentication et les services de fédération Active Directory (AD FS) vous permettent d’implémenter l’authentification unique entre les applications ou les services protégés par Azure AD (comme IdP) et les applications et services SAP protégés par SAP Cloud Platform Identity Authentication.

Actuellement, SAP Cloud Platform Identity Authentication remplit la fonction de fournisseur d’identité proxy pour les applications SAP. Azure Active Directory joue en retour le rôle de fournisseur d’identité principal dans cette configuration. 

Le schéma suivant illustre cette relation :

![Création d’un utilisateur de test Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Avec cette configuration, votre client SAP Cloud Platform Identity Authentication est configuré en tant qu’application approuvée dans Azure Active Directory.

Toutes les applications et tous les services SAP que vous souhaitez protéger de cette façon sont ensuite configurés dans la console de gestion SAP Cloud Platform Identity Authentication.

Ainsi, l’autorisation d’accorder l’accès aux services et applications SAP doit avoir lieu dans SAP Cloud Platform Identity Authentication (par opposition à Azure Active Directory).

Quand vous configurez SAP Cloud Platform Identity Authentication en tant qu’application par le biais de la Place de Marché Azure Active Directory, vous n’avez pas besoin de configurer des revendications ou des assertions SAML.

> [!NOTE]
> Actuellement, seule l’authentification unique web a été testée par les deux parties. Les flux nécessaires à la communication application-API ou API-API devraient fonctionner mais n’ont pas encore été testés. Ils seront testés durant les activités à venir.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie

Pour configurer l’intégration de SAP Cloud Platform Identity Authentication dans Azure AD, vous devez ajouter SAP Cloud Platform Identity Authentication à votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter SAP Cloud Platform Identity Authentication à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **SAP Cloud Platform Identity Authentication**, sélectionnez **SAP Cloud Platform Identity Authentication** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![SAP Cloud Platform Identity Authentication dans la liste de résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec [Nom de l’application] sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur [Nom de l’application] associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec [Nom de l’application], vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud Platform Identity Authentication, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec [Nom de l’application], procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **SAP Cloud Platform Identity Authentication**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<IAS-tenant-id>.accounts.ondemand.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Si vous ne comprenez pas la valeur d’identificateur, lisez la documentation SAP Cloud Platform Identity Authentication relative à la [configuration du locataire SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez-la à jour avec l’URL de connexion réelle. Utilisez l’URL de connexion spécifique de votre application métier. En cas de doute, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

6. L’application SAP Cloud Platform Identity Authentication attend les assertions SAML dans un format précis. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Si votre application SAP attend un attribut, tel que **firstName**, ajoutez l’attribut **firstName** dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez l’attribut du jeton SAML comme illustré dans l’image ci-dessus et effectuez les étapes suivantes :

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Nom**, tapez le nom d’attribut firstName.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, sélectionnez la valeur d’attribut user.givenname.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML des métadonnées** en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer SAP Cloud Platform Identity Authentication**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Configurer l’authentification unique SAP Cloud Platform Identity Authentication

1. Pour configurer l’authentification unique sur votre application, accédez à la console d’administration de SAP Cloud Platform Identity Authentication. L’URL suit le modèle suivant : `https://<tenant-id>.accounts.ondemand.com/admin`. Lisez ensuite la documentation relative à SAP Cloud Platform Identity Authentication à l’adresse [Integration with Microsoft Azure AD (Intégration à Microsoft Azure AD)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. Dans le portail Azure, sélectionnez le bouton **Enregistrer**.

3. Effectuez les étapes suivantes uniquement si vous souhaitez ajouter et activer l’authentification unique pour une autre application SAP. Répétez les étapes décrites dans la section **Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie**.

4. Dans le portail Azure, dans la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, sélectionnez **Authentification liée**.

    ![Configurer l’authentification liée](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Enregistrez la configuration.

> [!NOTE]
> La nouvelle application utilise la configuration de l’authentification unique de l’application SAP précédente. Veillez à utiliser les mêmes fournisseurs d’identité d’entreprise dans la console d’administration de SAP Cloud Platform Identity Authentication.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à SAP Cloud Platform Identity Authentication.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **SAP Cloud Platform Identity Authentication**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **SAP Cloud Platform Identity Authentication**.

    ![Lien SAP Cloud Platform Identity Authentication dans la liste Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Créer un utilisateur de test SAP Cloud Platform Identity Authentication

Vous n’avez pas besoin de créer un utilisateur dans SAP Cloud Platform Identity Authentication. Les utilisateurs qui se trouvent dans le magasin d’utilisateurs Azure AD peuvent utiliser la fonctionnalité d’authentification unique (SSO).

SAP Cloud Platform Identity Authentication prend en charge l’option de fédération des identités. Cette option permet à l’application de vérifier si les utilisateurs authentifiés par le fournisseur d’identité d’entreprise sont présents dans le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication.

L’option de fédération des identités est désactivée par défaut. Si la fédération des identités est activée, seuls les utilisateurs importés dans SAP Cloud Platform Identity Authentication peuvent accéder à l’application.

Pour savoir comment activer ou désactiver la fédération des identités avec SAP Cloud Platform Identity Authentication, consultez « Activer la fédération des identités avec SAP Cloud Platform Identity Authentication » dans [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Configurer la fédération des identités avec le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SAP Cloud Platform Identity Authentication dans le volet d’accès, vous devez vous connecter automatiquement à l’application SAP Cloud Platform Identity Authentication pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)