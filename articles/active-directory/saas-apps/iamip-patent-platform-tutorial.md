---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à IamIP Patent Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et IamIP Patent Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à IamIP Patent Platform

Dans ce tutoriel, vous allez découvrir comment intégrer IamIP Patent Platform à Azure Active Directory (Azure AD). Quand vous intégrez IamIP Patent Platform à Azure AD, vous pouvez :

* Utilisez Azure AD pour contrôler l’accès à IamIP Patent Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à IamIP Patent Platform avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration d’applications SaaS à Azure AD, consultez l’article [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement IamIP Patent Platform pour lequel l’authentification unique (SSO) est activée.

## <a name="tutorial-description"></a>Description du tutoriel

Dans ce tutoriel, vous allez configurer et tester l’authentification SSO Azure AD dans un environnement de test.

IamIP Patent Platform prend en charge l’authentification SSO lancée par le SP (fournisseur de services) et l’IdP (fournisseur d’identité).

Après avoir configuré IamIP Patent Platform, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Ajouter IamIP Patent Platform à partir de la galerie

Pour configurer l’intégration d’IamIP Patent Platform à Azure AD, vous devez ajouter Civic Platform à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire, ou avec un compte Microsoft personnel.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **IamIP Patent Platform** dans la zone de recherche.
1. Sélectionnez **IamIP Patent Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Configurer et tester l’authentification SSO Azure AD pour IamIP Patent Platform

Vous allez configurer et tester l’authentification SSO Azure AD avec IamIP Patent Platform à l’aide d’un utilisateur de test nommé B.Simon. Pour que l’authentification SSO fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans IamIP Patent Platform.

Pour configurer et tester l’authentification SSO Azure AD avec IamIP Patent Platform, suivez les étapes générales suivantes :

1. **[Configurer l’authentification SSO Azure AD](#configure-azure-ad-sso)** , pour permettre à vos utilisateurs d’utiliser la fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD.
    * **[Octroyer l’accès à l’utilisateur de test](#grant-access-to-the-test-user)** , pour lui permettre d’utiliser l’authentification unique Azure AD.

1. **[Configurer l’authentification SSO IamIP Patent Platform](#configure-iamip-patent-platform-sso)** côté application.
    * **[Créer un utilisateur de test IamIP Patent Platform](#create-iamip-patent-platform-test-user)** , pour avoir un équivalent qui soit lié à la représentation Azure AD de l’utilisateur.

1. **[Tester l’authentification SSO](#test-sso)** , pour vérifier que la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration d’application **IamIP Patent Platform**, dans la section **Gérer**, sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez le bouton représentant un crayon et correspondant à **Configuration SAML de base** pour modifier les paramètres :

   ![Bouton représentant un crayon pour la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un fichier de métadonnées de fournisseur de services, et si vous souhaitez configurer le mode authentification SSO lancée par l’IdP (fournisseur d’identité), effectuez les étapes suivantes :

    a. Sélectionnez **Charger le fichier de métadonnées** :

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Sélectionnez le bouton de dossier, sélectionnez le fichier de métadonnées, puis sélectionnez **Charger** :

    ![Bouton de dossier et bouton Charger](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont indiquées automatiquement dans la section **Configuration SAML de base** :

    ![Valeurs de l’identificateur et de l’URL de réponse](common/idp-intiated.png)

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement indiquées, spécifiez-les manuellement en fonction de vos besoins.

1. Si vous souhaitez configurer l’application en mode authentification lancée par le SP (fournisseur de services), sélectionnez **Définir des URL supplémentaires**, puis effectuez l’étape suivante :

    Dans la zone **URL de connexion**, entrez **https:\//patents.iamip.com/login-user**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez le lien **Télécharger** correspondant à **Certificat (brut)** pour télécharger le certificat et l’enregistrer sur votre ordinateur :

    ![Lien de téléchargement du certificat](common/certificateraw.png)

1. Dans la section **Configurer IamIP Patent Platform**, copiez les URL appropriées en fonction de vos besoins :

    ![Copier les URL de configuration](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**. Sélectionnez **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans la zone **Nom**, entrez **B.Simon**.  
   1. Dans la zone **Nom d’utilisateur**, entrez \<nom_utilisateur>@\<domaine_de_société>.\<extension>. Par exemple : `B.Simon@contoso.com`.
   1. Sélectionnez **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="grant-access-to-the-test-user"></a>Octroyer l’accès à l’utilisateur de test

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à IamIP Patent Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **IamIP Patent Platform**.
1. Dans la page de vue d’ensemble de l’application, dans la section **Gérer**, sélectionnez **Utilisateurs et groupes** :

   ![Sélectionner Utilisateurs et groupes](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution** :

    ![Sélectionnez Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-iamip-patent-platform-sso"></a>Configurer l’authentification unique IamIP Patent Platform

Pour configurer l’authentification unique côté IamIP Patent Platform, vous devez envoyer le certificat brut téléchargé et les URL appropriées que vous avez copiées à partir du portail Azure à l’[équipe du support technique de IamIP Patent Platform](mailto:info@iamip.com). Celle-ci configure la connexion SSO SAML de manière appropriée des deux côtés.

### <a name="create-iamip-patent-platform-test-user"></a>Créer un utilisateur de test IamIP Patent Platform

Collaborez avec l’[équipe du support technique de IamIP Patent Platform](mailto:info@iamip.com) pour ajouter un utilisateur nommé B.Simon à IamIP Patent Platform. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification SSO Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette IamIP Patent Platform dans le volet d’accès, vous êtes automatiquement connecté à l’instance de IamIP Patent Platform pour laquelle vous avez configuré l’authentification SSO. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer IamIP Patent Platform avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
