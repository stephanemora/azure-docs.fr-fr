---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à IamIP Patent Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et IamIP Patent Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2021
ms.author: jeedes
ms.openlocfilehash: 8022ea8ab38dd7b22fe7c51f2700321c40fd9333
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752518"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à IamIP Patent Platform

Dans ce tutoriel, vous allez découvrir comment intégrer IamIP Patent Platform à Azure Active Directory (Azure AD). Quand vous intégrez IamIP Patent Platform à Azure AD, vous pouvez :

* Utilisez Azure AD pour contrôler l’accès à IamIP Patent Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à IamIP Patent Platform avec leur compte Azure AD.
* gérer vos comptes à un emplacement central : le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement IamIP Patent Platform pour lequel l’authentification unique (SSO) est activée.

## <a name="tutorial-description"></a>Description du tutoriel

Dans ce tutoriel, vous allez configurer et tester l’authentification SSO Azure AD dans un environnement de test.

* IamIP Patent Platform prend en charge l’authentification SSO lancée par le SP (fournisseur de services) et l’IdP (fournisseur d’identité).

## <a name="add-iamip-patent-platform-from-the-gallery"></a>Ajouter IamIP Patent Platform à partir de la galerie

Pour configurer l’intégration d’IamIP Patent Platform à Azure AD, vous devez ajouter Civic Platform à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire, ou avec un compte Microsoft personnel.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **IamIP Patent Platform** dans la zone de recherche.
1. Sélectionnez **IamIP Patent Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Configurer et tester l’authentification SSO Azure AD pour IamIP Patent Platform

Vous allez configurer et tester l’authentification SSO Azure AD avec IamIP Patent Platform à l’aide d’un utilisateur de test nommé B.Simon. Pour que l’authentification SSO fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans IamIP Patent Platform.

Pour configurer et tester l’authentification unique Azure AD avec IamIP Patent Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification SSO Azure AD](#configure-azure-ad-sso)** , pour permettre à vos utilisateurs d’utiliser la fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD.
    1. **[Octroyer l’accès à l’utilisateur de test](#grant-access-to-the-test-user)** , pour lui permettre d’utiliser l’authentification unique Azure AD.

1. **[Configurer l’authentification SSO IamIP Patent Platform](#configure-iamip-patent-platform-sso)** côté application.
    1. **[Créer un utilisateur de test IamIP Patent Platform](#create-iamip-patent-platform-test-user)** , pour avoir un équivalent qui soit lié à la représentation de l’utilisateur Azure AD.

1. **[Tester l’authentification SSO](#test-sso)** , pour vérifier que la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure :

1. Dans le Portail Azure, dans la page d’intégration d’application **IamIP Patent Platform**, dans la section **Gérer**, sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez le bouton représentant un crayon et correspondant à **Configuration SAML de base** pour modifier les paramètres :

   ![Bouton représentant un crayon pour la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un fichier de métadonnées de fournisseur de services, et si vous souhaitez configurer le mode authentification SSO lancée par l’IdP (fournisseur d’identité), effectuez les étapes suivantes :

    a. Sélectionnez **Charger le fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Sélectionnez le bouton de dossier, sélectionnez le fichier de métadonnées, puis sélectionnez **Charger**.

    ![Bouton de dossier et bouton Charger](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont indiquées automatiquement dans la section **Configuration SAML de base**.

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement indiquées, spécifiez-les manuellement en fonction de vos besoins.

1. Si vous souhaitez configurer l’application en mode authentification lancée par le SP (fournisseur de services), sélectionnez **Définir des URL supplémentaires**, puis effectuez l’étape suivante :

    Dans la zone **URL de connexion**, tapez l’URL :  `https://patents.iamip.com/login-user`.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez le lien **Télécharger** correspondant à **Certificat (brut)** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien de téléchargement du certificat](common/certificateraw.png)

1. Dans la section **Configurer IamIP Patent Platform**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé B.Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**. Sélectionnez **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans la zone **Nom**, entrez **B.Simon**.  
   1. Dans la zone **Nom d’utilisateur**, entrez \<username>@\<companydomain>.\<extension>. Par exemple : `B.Simon@contoso.com`.
   1. Sélectionnez **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
   1. Sélectionnez **Create** (Créer).

### <a name="grant-access-to-the-test-user"></a>Octroyer l’accès à l’utilisateur de test

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui octroyant l’accès à IamIP Patent Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **IamIP Patent Platform**.
1. Dans la page de vue d’ensemble de l’application, dans la section **Gérer**, sélectionnez **Utilisateurs et groupes** :
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-iamip-patent-platform-sso"></a>Configurer l’authentification unique IamIP Patent Platform

Pour configurer l’authentification unique côté IamIP Patent Platform, vous devez envoyer le certificat brut téléchargé et les URL appropriées que vous avez copiées à partir du portail Azure à l’[équipe du support technique de IamIP Patent Platform](mailto:info@iamip.com). Celle-ci configure la connexion SSO SAML de manière appropriée des deux côtés.

### <a name="create-iamip-patent-platform-test-user"></a>Créer un utilisateur de test IamIP Patent Platform

Collaborez avec l’[équipe du support technique de IamIP Patent Platform](mailto:info@iamip.com) pour ajouter un utilisateur nommé B.Simon à IamIP Patent Platform. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion à IamIP Patent Platform, d’où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion IamIP Patent Platform pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le Portail Azure. Vous êtes alors connecté automatiquement à l’application IamIP Patent Platform pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la mosaïque IamIP Patent Platform dans le volet d’accès, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application IamIP Patent Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré IamIP Patent Platform, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).