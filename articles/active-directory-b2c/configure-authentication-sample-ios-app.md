---
title: Configurer l’authentification dans un exemple d’application iOS Swift à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application iOS Swift.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1e6a76b5a534bb127dfaaab8e787cb446430c8d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524434"
---
# <a name="configure-authentication-in-a-sample-ios-swift-application-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application iOS Swift à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application [iOS Swift](https://developer.apple.com/swift/) pour illustrer l’ajout d’une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications mobiles.

## <a name="overview"></a>Vue d'ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0, que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application mobile utilise la bibliothèque [MSAL](../active-directory/develop/msal-overview.md) avec le flux PKCE du code d’autorisation d’OpenID Connect. La bibliothèque MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications mobiles. 

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur ouvre l’application et sélectionne **Connexion**.
1. L’application ouvre le navigateur système de l’appareil mobile et démarre une demande d’authentification auprès d’Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte avec un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un code d’autorisation à l’application.
1. L’application :
    1. échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation ;
    1. lit les revendications du jeton d’ID ;
    1. stocke les jetons dans un cache en mémoire pour une utilisation ultérieure.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, inscrivez deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application mobile** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription de l’application, spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé par Azure AD B2C après s’être authentifié avec Azure AD B2C. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application mobile de façon unique. Par exemple, l’**ID d’application : 1**.

- L’inscription de **l’API web** permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un *ID d’application* qui identifie votre API web de façon unique.  Par exemple, l’**ID d’application : 2**. Accordez à votre application mobile (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 


Les diagrammes suivants décrivent l’inscription des applications et l’architecture de l’application.

![Le diagramme décrit une application mobile avec une API web, les inscriptions et les jetons.](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Se déconnecter

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute : 

- [Xcode](https://developer.apple.com/xcode/) 13 ou version ultérieure.
- Gestionnaire de dépendances [CocoaPods](https://cocoapods.org/) pour les projets Cocoa Swift et Objective-C.


## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Étape 2 : Inscrire des applications mobiles

Au cours de cette étape, créez l’inscription d’application de l’application mobile et de l’API web, puis spécifier les étendues de votre API web.

### <a name="21-register-the-web-api-app"></a>2.1 Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Configurer les étendues de l’application API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 Inscrire l’application mobile

Effectuez les étapes suivantes pour créer l’inscription de l’application mobile :

1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *iOs-app1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Client public/natif (mobile et bureau)** , puis entrez : `msauth.com.microsoft.identitysample.MSALiOS://auth`.
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez **l’ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application mobile.
    ![Capture d’écran montrant comment récupérer l’ID de l’application mobile.](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Accorder à l’application mobile les autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Étape 3 : Configurer l’exemple d’API web

Cet exemple acquiert un jeton d’accès avec les étendues appropriées que l’application mobile peut utiliser pour une API web.  Pour appeler une API web à partir du code, procédez comme suit :

1. Utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).
1. Modifiez l’exemple de code pour [appeler une API web](enable-authentication-iOs-app.md#call-a-web-api).

Après avoir configuré l’API web, copiez l’URI du point de terminaison de l’API web. Vous allez utiliser le point de terminaison de l’API web dans les étapes suivantes.

> [!TIP]
> Si vous n’avez pas d’API web, vous pouvez quand même exécuter cet exemple. Dans ce cas, l’application renvoie le jeton d’accès, mais ne pourra pas appeler l’API web. 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>Étape 4 : Obtenir l’exemple d’application mobile iOS

1. [Téléchargez le fichier zip](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip) ou clonez l’exemple d’application web à partir du [référentiel GitHub](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. Utilisez [CocoaPods](https://cocoapods.org/) pour installer la bibliothèque MSAL. Dans une fenêtre de terminal, accédez au dossier racine du projet. Ce dossier contient l’élément `podfile`. Exécutez la commande suivante :

    ```bash
    pod install
    ```

1. Ouvrez l’espace de travail `MSALiOS.xcworkspace` avec Xcode.



## <a name="step-5-configure-the-sample-mobile-app"></a>Étape 5 : Configurer l’exemple d’application mobile

Ouvrez le fichier `ViewController.swift` . Les membres de la classe `ViewController` contiennent des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter l’utilisateur, acquérir des jetons et les valider. 

Mettez à jour les membres suivants :

|Clé  |Valeur  |
|---------|---------|
|kTenantName| Le [nom de locataire](tenant-management.md#get-your-tenant-name) complet de votre locataire Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`.|
|kAuthorityHostName|La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `contoso.b2clogin.com`.|
|kClientID|L’ID d’application mobile de l’[étape 2.3](#23-register-the-mobile-app).|
|kRedirectUri|L’URI de redirection de l’application mobile de l’[étape 2.3](#23-register-the-mobile-app), `msauth.com.microsoft.identitysample.MSALiOS://auth`.|
|kSignupOrSigninPolicy| Le flux d’utilisateur ou la stratégie personnalisée d’inscription ou de connexion que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|kEditProfilePolicy|Le flux d’utilisateur ou la stratégie personnalisée du profil de modification que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|kGraphURI| (Facultatif) Le point de terminaison de l’API web que vous avez créé à l’[étape 3](#step-3-configure-the-sample-web-api). Par exemple : `https://contoso.azurewebsites.net/hello`.|
| kScopes | Les étendues de l’API web créées à l’[étape 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api).| 



## <a name="step-6-run-and-test-the-mobile-app"></a>Étape 6 : Exécuter et tester l’application mobile

1. Générez et exécutez le projet avec un [simulateur d’appareil iOS connecté](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device).

1. Sélectionnez **Connexion**. Puis inscrivez-vous ou connectez-vous avec votre compte Azure AD B2C local ou avec un compte social.

    ![La capture d’écran montre comment démarrer le processus de connexion.](./media/configure-authentication-sample-ios-app/sign-in.png)

1. Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation.

    ![Capture d’écran montrant le jeton d’accès et l’identifiant utilisateur d’Azure AD B2C.](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [activer l’authentification dans votre propre application iOS](enable-authentication-ios-app.md)
* [Configurer les options d’authentification dans une application iOS](enable-authentication-ios-app-options.md)
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md)
