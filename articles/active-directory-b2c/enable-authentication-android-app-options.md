---
title: Activer les options d’application mobile Android avec Azure Active Directory B2C
description: Activez l’utilisation des options d’application mobile Android à l’aide de plusieurs méthodes.
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
ms.openlocfilehash: fc9e7a8dde4396933c244ad1d4b4a01d5003057c
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297917"
---
# <a name="configure-authentication-options-in-an-android-application-using-azure-active-directory-b2c"></a>Configurer les options d’authentification dans une application Android avec Azure Active Directory B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application Android. Avant de commencer, familiarisez-vous avec les articles suivants : [Configurer l’authentification dans un exemple d’application Android](configure-authentication-sample-android-app.md) ou [Activer l’authentification dans votre application Android à l’aide d’Azure Active Directory B2C](enable-authentication-android-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et l’ID de votre locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer les domaines personnalisés](custom-domain.md). Accédez à votre objet de configuration MSAL afin de remplacer la valeur de **authorities** par le nom de domaine personnalisé et l’ID de locataire.


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

Le code Kotlin suivant montre l’objet de configuration MSAL avant modification :

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

Le code Kotlin suivant montre l’objet de configuration MSAL après modification :

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

Le code Kotlin suivant montre l’objet de configuration MSAL avant modification :

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
Le code Kotlin suivant montre l’objet de configuration MSAL après modification :

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Accédez à votre objet de configuration MSAL pour ajouter la méthode **withLoginHint()** avec l’indicateur de connexion.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `domain_hint` avec le nom de domaine correspondant. Par exemple : `facebook.com`.
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `ui_locales` avec le code langue correspondant. Par exemple : `en-us`.
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définissez la valeur du paramètre. Par exemple : `germany-promotion`.
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Dans votre code, générez ou obtenez un jeton d’ID, puis définissez le jeton sur une variable. Par exemple : `idToken`. 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre `id_token_hint` avec la variable correspondante qui stocke le jeton d’ID.
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 



## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus : [Options de configuration MSAL pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)
