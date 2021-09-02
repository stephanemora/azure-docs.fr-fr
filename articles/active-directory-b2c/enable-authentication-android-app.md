---
title: Activer l’authentification dans une application Android – Azure AD B2C
description: Activez l’authentification dans une application Android à l’aide de blocs de construction Azure Active Directory B2C. Découvrez comment utiliser Azure AD B2C pour connecter et inscrire des utilisateurs dans une application Android.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/06/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support, has-adal-ref
ms.openlocfilehash: b9341909f6dd1dd9d01408a7b7af46e47a0ff339
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535177"
---
# <a name="enable-authentication-in-your-own-android-application-using-azure-active-directory-b2c"></a>Activer l’authentification dans votre propre application Android à l’aide d’Azure Active Directory B2C

Cet article explique comment ajouter l’authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application mobile Android. 

Utilisez cet article avec les instructions [Configuration de l’authentification dans un exemple d’application Android ](./configure-authentication-sample-android-app.md), en remplaçant l’exemple d’application Android par votre propre application Android. Une fois que vous avez effectué les étapes décrites dans cet article, votre application doit accepter les connexions via Azure AD B2C.

## <a name="prerequisites"></a>Configuration requise

Passez en revue les conditions préalables et les étapes d’intégration dans l’article [Configurer l’authentification dans un exemple d’application Android](configure-authentication-sample-android-app.md).

## <a name="create-an-android-app-project"></a>Créer un projet d’application Android

Si vous n’avez pas encore d’application Android, procédez comme suit pour configurer un nouveau projet.

1. Ouvrez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio**.
2. Sélectionnez **Activité de base**, puis **Suivant**.
3. Donnez un nom à votre application.
4. Enregistrez le nom du package. Vous l’entrerez ultérieurement dans le portail Azure.
5. Remplacez le langage **Kotlin** par **Java**.
6. Définissez le **Niveau d’API minimal** sur **API 19** ou sur une valeur supérieure, puis sélectionnez **Terminer**.
7. Dans la vue du projet, dans la liste déroulante, choisissez **Projet** pour afficher les fichiers projet sources et non-sources, ouvrez **app/build.gradle**, puis définissez `targetSdkVersion` sur `28`.

## <a name="install-the-dependencies"></a>Installer les dépendances

Dans la fenêtre de projet Android Studio, accédez à **app** > **build.gradle**, puis ajoutez ce qui suit :

```gradle
apply plugin: 'com.android.application'

allprojects {
    repositories {
    mavenCentral()
    google()
    mavenLocal()
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    maven {
        name "vsts-maven-adal-android"
        url "https://identitydivision.pkgs.visualstudio.com/_packaging/AndroidADAL/maven/v1"
        credentials {
            username System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") : project.findProperty("vstsUsername")
            password System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") : project.findProperty("vstsMavenAccessToken")
        }
    }
    jcenter()
    }
}
dependencies{
    implementation 'com.microsoft.identity.client:msal:2.+'
    }
packagingOptions{
    exclude("META-INF/jersey-module-version")
}
```


## <a name="add-the-authentication-components"></a>Ajouter les composants d’authentification

[L’exemple de code](configure-authentication-sample-android-app.md#step-3-get-the-android-mobile-app-sample) est constitué des composants suivants. Ajoutez ces composants de l’exemple d’application Android à votre propre application. 

|Composant  |Type  | Source |Description  |
|---------|---------|---------|---------|
| B2CUser| Classe| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CUser.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CUser.java)| Représente un utilisateur B2C. Cette classe permet aux utilisateurs de se connecter avec plusieurs stratégies. | 
| B2CModeFragment | Classe de fragment| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CModeFragment.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CModeFragment.java)| Un fragment représente une partie modulaire de la connexion auprès de l’interface utilisateur Azure AD B2C au sein de votre activité principale. Ce fragment contient la majeure partie du code d’authentification. |
| fragment_b2c_mode.xml | Disposition du fragment| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) | Définit la structure d’une interface utilisateur pour le composant de fragment B2CModeFragment. |
| B2CConfiguration| Classe| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CConfiguration.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CConfiguration.java)| Un fichier de configuration contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter l’utilisateur, acquérir des jetons et les valider. Pour obtenir d’autres paramètres de configuration, consultez le fichier auth_config_b2c.json.  | 
|auth_config_b2c.json | Fichier JSON| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/raw/auth_config_b2c.json) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/raw/auth_config_b2c.json)| Un fichier de configuration contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter l’utilisateur, acquérir des jetons et les valider. Pour les autres paramètres de configuration, consultez la classe B2CConfiguration. | 

## <a name="configure-your-android-app"></a>Configurer votre application Android

Après avoir [ajouté les composants d’authentification](#add-the-authentication-components), configurez votre application Android avec vos paramètres Azure AD B2C. Les paramètres du fournisseur d’identité Azure AD B2C sont configurés dans le fichier auth_config_b2c.json et la classe B2CConfiguration. 

Suivez les instructions expliquant comment [Configurer l’exemple d’application mobile](configure-authentication-sample-android-app.md#step-5-configure-the-sample-mobile-app).

## <a name="set-the-redirect-uri"></a>Définir l’URI de redirection

Dans cette section, configurez l’emplacement où votre application écoute la réponse de jeton Azure AD B2C. 


1. Générez un nouveau hachage de signature de développement. Il est différent pour chaque environnement de développement.
    
    Sur les systèmes d'exploitation Windows :
    
    ```
    keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    Sur les systèmes d’exploitation IOS :
    
    ```dotnetcli
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    Pour un environnement de production, utilisez la commande suivante :
    
    ```
    keytool -exportcert -alias SIGNATURE_ALIAS -keystore PATH_TO_KEYSTORE | openssl sha1 -binary | openssl base64
    ```

    Pour plus d’informations sur la signature de vos applications, consultez [Signature de votre application Android](https://developer.android.com/studio/publish/app-signing). 

1. Dans **app** > **src** > **main** > **AndroidManifest.xml**, ajoutez l’activité `BrowserTabActivity` ci-dessous au corps de l’application :
    
    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Package_Name"
                android:path="/Signature_Hash" />
        </intent-filter>
    </activity>
    ```
1. Remplacez `Signature_Hash` par le hachage que vous avez généré.
1. Remplacez `Package_Name` par le nom de votre nom du package Android.
 
Procédez comme suit pour mettre à jour l’inscription de l’application mobile avec l’URI de redirection de votre application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez l’application que vous avez inscrite à l’étape [2.3 Inscrire l’application mobile](configure-authentication-sample-android-app.md#23-register-the-mobile-app).
1. Sélectionnez **Authentification**.
1. Sous **Android**, sélectionnez **Ajouter l’URI**.
1. Entrez le **Nom du package** et **Hachage de signature**.
1. Sélectionnez **Enregistrer**.

Votre URI de redirection et l’activité `BrowserTabActivity` doivent ressembler à l’exemple suivant : 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

URL de redirection pour l’exemple Android :

```
msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

Le filtre d’intention utilise ensuite le même modèle, comme dans l’extrait de code XML suivant :

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="com.azuresamples.msalandroidkotlinapp"
            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
            android:scheme="msauth" />
    </intent-filter>
</activity>
```



#### <a name="java"></a>[Java](#tab/java)

URL de redirection pour l’exemple Android :

```
msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

Le filtre d’intention utilise ensuite le même modèle, comme dans l’extrait de code XML suivant :

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:host="com.azuresamples.msalandroidapp"
        android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
        android:scheme="msauth" />
</intent-filter>
</activity>
```

--- 

## <a name="code-building-blocks"></a>Blocs de construction de code

Cette section décrit les blocs de construction de code qui permettent l’authentification pour votre application Android. Le tableau suivant répertorie les méthodes B2CModeFragment et explique comment personnaliser votre code. 

### <a name="instantiate-a-public-client-application"></a>Instancier une application cliente publique

Les applications clientes publiques ne sont pas approuvées pour conserver en toute sécurité les secrets d’application. Elles ne disposent pas de clé secrète client. Dans [onCreate](https://developer.android.com/reference/android/app/Fragment#onCreate(android.os.Bundle)) ou [onCreateView](https://developer.android.com/reference/android/app/Fragment#onCreateView(android.view.LayoutInflater,%20android.view.ViewGroup,%20android.os.Bundle)), instanciez MSAL à l’aide de l’objet d’application cliente publique à plusieurs comptes.

La classe `MultipleAccountPublicClientApplication` est utilisée pour créer des applications MSAL qui autorisent plusieurs comptes à se connecter en même temps. Elle permet la connexion avec plusieurs flux d’utilisateurs ou stratégies personnalisées Azure AD B2C. Par exemple, un utilisateur se connecte par flux d’utilisateur [inscription ou connexion](add-sign-up-and-sign-in-policy.md), et exécute ultérieurement un flux d’utilisateur [modifier le profil](add-profile-editing-policy.md). 

L’extrait de code suivant montre comment initier la bibliothèque MSAL avec le fichier JSON `auth_config_b2c.json` de configuration.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
PublicClientApplication.createMultipleAccountPublicClientApplication(context!!,
    R.raw.auth_config_b2c,
    object : IMultipleAccountApplicationCreatedListener {
        override fun onCreated(application: IMultipleAccountPublicClientApplication) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application
            // Load the account (if there is any)
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            // Error handling
            displayError(exception)
        }
    })
```

#### <a name="java"></a>[Java](#tab/java)

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
    R.raw.auth_config_b2c,
    new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication application) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application;

            // Load the account (if there is any)
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            // Error handling
            displayError(exception);
        }
    });
```

--- 

### <a name="load-accounts"></a>Charger des comptes

Quand l’application passe au premier plan, l’application charge le compte existant pour déterminer si l’utilisateur est connecté ou pas.  Utilisez cette méthode pour mettre à jour l’interface utilisateur avec l’état d’authentification. Par exemple, activez ou désactivez le bouton de déconnexion.

L’extrait de code suivant illustre comment charger les comptes.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private fun loadAccounts() {
    if (b2cApp == null) {
        return
    }
    b2cApp!!.getAccounts(object : LoadAccountsCallback {
        override fun onTaskCompleted(result: List<IAccount>) {
            users = B2CUser.getB2CUsersFromAccountList(result)
            updateUI(users)
        }
    
        override fun onError(exception: MsalException) {
            displayError(exception)
        }
    })
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private void loadAccounts() {
    if (b2cApp == null) {
        return;
    }

    b2cApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            users = B2CUser.getB2CUsersFromAccountList(result);
            updateUI(users);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

--- 

### <a name="interactive-authorization-request"></a>Requête d’autorisation interactive

Une requête d’autorisation interactive est un flux dans lequel l’utilisateur est invité à s’inscrire ou à se connecter. La méthode `initializeUI` configure l’événement de clic `runUserFlowButton`. Quand l’utilisateur sélectionne le bouton **EXÉCUTER LE FLUX D’UTILISATEUR**, l’application envoie l’utilisateur vers Azure AD B2C pour qu’il effectue le processus de connexion. 

La méthode `runUserFlowButton.setOnClickListener` prépare l’objet `AcquireTokenParameters` avec les données pertinentes relatives à la requête d’autorisation. La méthode `acquireToken` invite l’utilisateur à réaliser le processus d’inscription ou de connexion. 

L’extrait de code suivant montre comment démarrer la requête d’autorisation interactive. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority(getAuthorityFromPolicyName(policy_list.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.scopes)
        .withPrompt(Prompt.LOGIN)
        .withCallback(authInteractiveCallback)
        .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(getActivity())
        .fromAuthority(B2CConfiguration.getAuthorityFromPolicyName(policyListSpinner.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.getScopes())
        .withPrompt(Prompt.LOGIN)
        .withCallback(getAuthInteractiveCallback())
        .build();

b2cApp.acquireToken(parameters);
```

--- 

 
### <a name="interactive-authorization-request-callback"></a>Rappel de requête d’autorisation interactive

Une fois que l’utilisateur a terminé le flux d’autorisation (avec ou sans succès), le résultat est retourné à la méthode de rappel `getAuthInteractiveCallback()`. 

La méthode de rappel transmet l’objet `AuthenticationResult` ou un message d’erreur dans l’objet `MsalException`. Utilisez cette méthode pour :

- Mettre à jour l’interface utilisateur de l’application mobile avec les informations une fois la connexion effectuée
- Recharger l’objet des comptes
- Appeler un service d’API web avec un jeton d’accès
- Gérer les erreurs d’authentification

L’extrait de code suivant illustre l’utilisation du rappel d’authentification interactive.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private val authInteractiveCallback: AuthenticationCallback
    private get() = object : AuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            /* Successfully got a token, use it to call a protected resource; web API  */
            Log.d(TAG, "Successfully authenticated")

            /* display result info */
            displayResult(authenticationResult)

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            val B2C_PASSWORD_CHANGE = "AADB2C90118"
            if (exception.message!!.contains(B2C_PASSWORD_CHANGE)) {
                txt_log!!.text = """
                    The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.
                    Your application needs to handle this error code by running a specific user flow that resets the password.
                    """.trimIndent()
                return
            }

            /* Failed to acquireToken */Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        override fun onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.")
        }
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");

            /* display result info */
            displayResult(authenticationResult);

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            final String B2C_PASSWORD_CHANGE = "AADB2C90118";
            if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
                logTextView.setText("The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.\n" +
                        "Your application needs to handle this error code by running a specific user flow that resets the password.");
                return;
            }

            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

--- 

## <a name="call-a-web-api"></a>Appeler une API web

Pour appeler une [API web d’autorisation basée sur les jetons](enable-authentication-web-api.md), l’application doit disposer d’un jeton d’accès valide. L’application effectue les étapes suivantes :


1. Elle obtient un jeton d’accès avec les autorisations requises (étendues) pour le point de terminaison de l’API web.
1. Elle transmet le jeton d’accès en tant que jeton du porteur dans l’en-tête d’autorisation de la requête HTTP en utilisant le format suivant :

```http
Authorization: Bearer <access-token>
```

Quand les utilisateurs [se connectent de manière interactive](#interactive-authorization-request), l’application obtient un jeton d’accès dans la méthode de rappel `getAuthInteractiveCallback`. Pour les appels d’API web consécutifs, utilisez la procédure d’obtention de jeton sans assistance, comme décrit dans cette section. 

Avant d’appeler une API web, appelez la méthode `acquireTokenSilentAsync` avec les étendues appropriées pour votre point de terminaison d’API web. La bibliothèque MSAL effectue les étapes suivantes :

1. Elle essaie de récupérer (fetch) un jeton d’accès avec les étendues demandées à partir du cache de jeton. S’il est présent, le jeton est retourné. 
1. Si le jeton n’est pas présent dans le cache de jeton, la bibliothèque MSAL tente d’utiliser son jeton d’actualisation pour obtenir un nouveau jeton. 
1. Si le jeton d’actualisation n’existe pas ou a expiré, une exception est retournée. Il est recommandé d’inviter l’utilisateur à [se connecter de manière interactive](#interactive-authorization-request).  

L’extrait de code suivant montre comment obtenir un jeton d’accès :

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

L’événement de clic sur le bouton `acquireTokenSilentButton` permet d’obtenir un jeton d’accès avec les étendues fournies. 

```kotlin
btn_acquireTokenSilently.setOnClickListener(View.OnClickListener {
    if (b2cApp == null) {
        return@OnClickListener
    }
    val selectedUser = users!![user_list.getSelectedItemPosition()]
    selectedUser.acquireTokenSilentAsync(b2cApp!!,
            policy_list.getSelectedItem().toString(),
            B2CConfiguration.scopes,
            authSilentCallback)
})
```

La méthode de rappel `authSilentCallback` retourne un jeton d’accès et appelle une API web :

```kotlin
private val authSilentCallback: SilentAuthenticationCallback
    private get() = object : SilentAuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            Log.d(TAG, "Successfully authenticated")

            /* Call your web API here*/
            callWebAPI(authenticationResult)
        }

        override fun onError(exception: MsalException) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception is MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    }
```

L’exemple suivant montre comment appeler une API web protégée avec un jeton du porteur :

```kotlin
@Throws(java.lang.Exception::class)
private fun callWebAPI(authenticationResult: IAuthenticationResult) {
    val accessToken = authenticationResult.accessToken
    val thread = Thread {
        try {
            val url = URL("https://your-app-service.azurewebsites.net/helo")
            val conn = url.openConnection() as HttpsURLConnection
            conn.setRequestProperty("Accept", "application/json")
            
            // Set the bearer token
            conn.setRequestProperty("Authorization", "Bearer $accessToken")
            if (conn.responseCode == HttpURLConnection.HTTP_OK) {
                val br = BufferedReader(InputStreamReader(conn.inputStream))
                var strCurrentLine: String?
                while (br.readLine().also { strCurrentLine = it } != null) {
                    Log.d(TAG, strCurrentLine)
                }
            }
            conn.disconnect()
        } catch (e: IOException) {
            e.printStackTrace()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
    thread.start()
}
```

#### <a name="java"></a>[Java](#tab/java)

L’événement de clic sur le bouton `acquireTokenSilentButton` permet d’obtenir un jeton d’accès avec les étendues fournies. 

```java
acquireTokenSilentButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        if (b2cApp == null) {
            return;
        }

        final B2CUser selectedUser = users.get(b2cUserList.getSelectedItemPosition());
        selectedUser.acquireTokenSilentAsync(b2cApp,
                policyListSpinner.getSelectedItem().toString(),
                B2CConfiguration.getScopes(),
                getAuthSilentCallback());
    }
});
```

La méthode de rappel `authSilentCallback` retourne un jeton d’accès et appelle une API web :

```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Call your web API here*/
            callWebAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

L’exemple suivant montre comment appeler une API web protégée avec un jeton du porteur :

```java
private void callWebAPI(IAuthenticationResult authenticationResult) throws Exception {
    final String accessToken = authenticationResult.getAccessToken();
    
    
    Thread thread = new Thread(new Runnable() {
    
        @Override
        public void run() {
            try {
                URL url = new URL("https://your-app-service.azurewebsites.net/helo");
                HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
                conn.setRequestProperty("Accept", "application/json");
                
                // Set the bearer token
                conn.setRequestProperty("Authorization", "Bearer " +  accessToken);
    
                if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                    String strCurrentLine;
                    while ((strCurrentLine = br.readLine()) != null) {
                        Log.d(TAG, strCurrentLine);
                    }
                }
                conn.disconnect();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
    
    thread.start();
    
    }
```

--- 

### <a name="add-permission-to-perform-network-operations"></a>Ajouter l’autorisation d’effectuer des opérations sur le réseau

Pour effectuer des opérations de réseau dans votre application, incluez l’autorisation suivante à votre manifeste. Pour plus d’informations, consultez [Se connecter au réseau](https://developer.android.com/training/basics/network-ops/connecting).

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## <a name="next-steps"></a>Étapes suivantes

* [Configurer les options d’authentification dans une application Android](enable-authentication-android-app-options.md)
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md)
