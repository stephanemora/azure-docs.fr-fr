---
title: Configurer l’authentification dans un exemple d’application Android à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application Android.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2021
ms.locfileid: "122562102"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application Android à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application Android (Kotlin et Java) pour illustrer l’ajout de l’authentification Azure Active Directory B2C (Azure AD B2C) à vos applications mobiles.

## <a name="overview"></a>Vue d'ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0, que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application mobile utilise la bibliothèque [MSAL](../active-directory/develop/msal-overview.md) avec le flux PKCE du code d’autorisation d’OpenID Connect. La bibliothèque MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation dans les applications mobiles. 

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur ouvre l’application et sélectionne **Connexion**.
1. L’application ouvre le navigateur système de l’appareil mobile et démarre une demande d’authentification auprès d’Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte avec un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un code d’autorisation à l’application.
1. L’application :
    1. échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation ;
    1. lit les revendications du jeton d’ID ;
    1. stocke les jetons dans un cache en mémoire pour une utilisation ultérieure.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, inscrivez deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application mobile** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription de l’application, spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé par Azure AD B2C une fois son authentification avec Azure AD B2C terminée. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application mobile de façon unique. Par exemple, l’**ID d’application : 1**.

- L’inscription de l’**API web** permet à votre application d’appeler une API web protégée. Cette inscription expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un *ID d’application* qui identifie votre API web de façon unique.  Par exemple, l’**ID d’application : 2**. Accordez à votre application mobile (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 


Les diagrammes suivants décrivent l’inscription des applications et l’architecture de l’application.

![Application mobile avec inscriptions et jetons d’appel d’API web](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Se déconnecter

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute : 


- [Kit de développement Java (JDK)](https://openjdk.java.net/) 8 ou ultérieur.
- [Apache Maven](https://maven.apache.org/)
- [Niveau d’API Android 16](https://developer.android.com/studio/releases/platforms) ou supérieur.
- [Android Studio](https://developer.android.com/studio) ou un autre éditeur de code.


## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Étape 2 : Inscrire des applications mobiles

Au cours de cette étape, créez l’inscription de l’application mobile et de l’application d’API web, puis spécifiez les étendues de votre API web.

### <a name="21-register-the-web-api-app"></a>2.1 Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Configurer les étendues de l’application API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 Inscrire l’application mobile

Effectuez les étapes suivantes pour créer l’inscription de l’application mobile :

1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *android-app1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Client public/natif (mobile et bureau)** , puis, dans la zone de texte de l’URL, entrez l’un des URI suivants :
    - Pour l’exemple Kotlin : `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Pour l’exemple Java : `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application mobile.

    ![Obtenir votre ID d’application mobile](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Accorder à l’application mobile les autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Étape 3 : Obtenir l’exemple d’application mobile Android

Téléchargez l’un des exemples suivants : [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) ou [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip). Extrayez l’exemple de fichier ZIP dans votre dossier de travail.

Ou clonez l’exemple d’application mobile Android à partir de GitHub. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```bash
git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
```

#### <a name="java"></a>[Java](#tab/java)

```bash
git clone https://github.com/Azure-Samples/ms-identity-android-java
```

--- 


## <a name="step-4-configure-the-sample-web-api"></a>Étape 4 : Configurer l’exemple d’API web

Cet exemple acquiert un jeton d’accès avec les étendues appropriées que l’application mobile peut utiliser pour une API web. Pour appeler une API web à partir du code, procédez comme suit :

1. Utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).
1. Modifiez l’exemple de code pour [appeler une API web](enable-authentication-android-app.md#call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Étape 5 : Configurer l’exemple d’application mobile

Ouvrez l’exemple de projet avec Android Studio ou un autre éditeur de code.  Ouvrez ensuite le fichier `/app/src/main/res/raw/auth_config_b2c.json`. 

Le fichier de configuration *auth_config_b2c.json* contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation d’approbation avec Azure AD B2C, connecter et déconnecter l’utilisateur, acquérir des jetons et les valider. 

Mettez à jour les propriétés suivantes des paramètres de l’application :

|Clé  |Valeur  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | L’ID d’application mobile de l’[étape 2.3](#23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | L’URI de redirection de l’application mobile de l’[étape 2.3](#23-register-the-mobile-app). | 
| [autorités](../active-directory/develop/msal-client-application-configuration.md#authority)| L’autorité est une URL indiquant un annuaire dont la bibliothèque d’authentification Microsoft peut demander des jetons. Utilisez le format suivant : `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Remplacez `<your-tenant-name>` par votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Ensuite, remplacez `<your-sign-in-sign-up-policy>` par les flux d’utilisateur ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow). | 


Ouvrez la classe `B2CConfiguration` et mettez à jour les membres de classe suivants :

|Clé  |Valeur  |
|---------|---------|
| Stratégies| Liste des flux d’utilisateur ou des stratégies personnalisées que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
| azureAdB2CHostName| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `https://contoso.b2clogin.com`.|
| tenantName| Le [nom de locataire](tenant-management.md#get-your-tenant-name) complet de votre locataire Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`.|
| étendues| Les étendues de l’API web que vous avez créées à l’[étape 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api).|


## <a name="step-6-run-and-test-the-mobile-app"></a>Étape 6 : Exécuter et tester l’application mobile

1. Générez et exécutez le projet.
1. Sélectionnez l’icône de hamburger.
    
    ![Capture d’écran montrant comment sélectionner l’icône de hamburger](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. Sélectionnez **Mode B2C**.

    ![Capture d’écran montrant comment sélectionner Mode B2C](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Sélectionnez **EXÉCUTER LE FLUX D’UTILISATEUR**.

    ![Capture d’écran montrant comment démarrer le flux de connexion](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Inscrivez-vous ou connectez-vous avec votre compte social ou local Azure AD B2C.

1. Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation.

    ![Jeton d’accès et ID d’utilisateur Azure AD B2C](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [activer l’authentification dans votre propre application Android](enable-authentication-android-app.md)
* [Configurer les options d’authentification dans une application Android](enable-authentication-android-app-options.md)
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md)
