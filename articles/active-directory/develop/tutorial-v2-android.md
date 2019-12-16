---
title: Connecter des utilisateurs et appeler Microsoft Graph (Android) - Plateforme d’identités Microsoft | Azure
description: Obtenir un jeton d’accès et appeler Microsoft Graph ou des API qui nécessitent des jetons d’accès de la plateforme d’identités Microsoft (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feefc368815b1bfe57b67db2cd94702db799d78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961555"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Didacticiel : Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application Android

> [!NOTE]
> Ce tutoriel n’a pas encore été mis à jour pour fonctionner avec la bibliothèque MSAL pour Android version 1.0. Il fonctionne avec une version antérieure, telle que configurée ci-après.

Dans ce tutoriel, vous allez apprendre à intégrer une application Android à la plateforme d’identités Microsoft. Votre application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph.  

> [!div class="checklist"]
> * Intégrer une application Android à la plateforme d’identités Microsoft
> * Connecter un utilisateur
> * Obtenir un jeton d’accès pour appeler l’API Microsoft Graph
> * Appeler l’API Microsoft Graph  

À la fin de ce tutoriel, votre application acceptera les connexions de comptes Microsoft personnels (notamment outlook.com, live.com et d’autres) ainsi que de comptes professionnels ou scolaires de toute entreprise ou organisation utilisant Azure Active Directory.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="how-this-tutorial-works"></a>Fonctionnement de ce tutoriel

![Fonctionnement de l’exemple d’application généré par ce tutoriel](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L’application utilisée dans ce tutoriel est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers.  Ces données sont accessibles par le biais d’une API protégée (l’API Microsoft Graph) qui nécessite une autorisation et est protégée par la plateforme d’identités Microsoft.

Plus précisément :

* Votre application connectera l’utilisateur via un navigateur ou Microsoft Authenticator et le portail d’entreprise Intune.
* L’utilisateur final acceptera les autorisations que votre application a demandées.
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft pour Android (MSAL) pour implémenter l’authentification : [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL renouvelle automatiquement les jetons, fournit une authentification unique (SSO) entre les autres applications de l’appareil et gère les comptes.

## <a name="prerequisites"></a>Prérequis

* Ce tutoriel nécessite Android Studio version 3.5.

## <a name="create-a-project"></a>Création d’un projet

Ce tutoriel va créer un projet. Si vous souhaitez plutôt télécharger le tutoriel complet, [téléchargez le code](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).

1. Ouvrez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio**.
2. Sélectionnez **Activité de base**, puis **Suivant**.
3. Donnez un nom à votre application.
4. Enregistrez le nom du package. Vous allez l’entrer plus tard dans le portail Azure.
5. Remplacez le langage **Kotlin** par **Java**.
6. Affectez à **Niveau d’API minimal** la valeur **API 19** ou une valeur supérieure, puis cliquez sur **Terminer**.
7. Dans la vue du projet, dans la liste déroulante, choisissez **Projet** pour afficher les fichiers projet sources et non-sources, ouvrez **app/build.gradle**, puis affectez à `targetSdkVersion` la valeur `28`.

## <a name="register-your-application"></a>Inscrivez votre application

1. Accédez au [portail Azure](https://aka.ms/MobileAppReg).
2. Ouvrez le panneau [Inscriptions d’applications](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), puis cliquez sur **+Nouvelle inscription**.
3. Entrez un **Nom** pour votre application, puis, sans définir d’URI de redirection, cliquez sur **Inscrire**.
4. Dans la section **Gérer** du volet qui apparaît, sélectionnez **Authentification** >  **+ Ajouter une plateforme** > **Android**. (Vous devrez peut-être sélectionner « Essayer la nouvelle expérience » près du haut du panneau pour voir cette section.)
5. Entrez le nom du package de votre projet. Si vous avez téléchargé le code, cette valeur est `com.azuresamples.msalandroidapp`.
6. Dans la section **Hachage de signature** de la page **Configurer votre application Android**, cliquez sur **Création d’un hachage de signature de développement** et copiez la commande KeyTool à utiliser pour votre plateforme.

   > [!Note]
   > Keytool.exe est installé en même temps que le kit de développement Java (JDK). Vous devez également installer l’outil OpenSSL pour exécuter la commande KeyTool.

7. Entrez le **Hachage de signature** généré par KeyTool.
8. Cliquez sur `Configure` et enregistrez la **Configuration MSAL** qui apparaît dans la page **Configuration Android** pour pouvoir l’entrer plus tard quand vous devrez configurer votre application.  Cliquez sur **Done**.

## <a name="build-your-app"></a>Générer votre application

### <a name="add-your-app-registration"></a>Ajouter votre inscription d’application

1. Dans le volet de projet d’Android Studio, accédez à **app\src\main\res**.
2. Cliquez avec le bouton droit sur **res**, puis choisissez **Nouveau** > **Répertoire**. Entrez `raw` en tant que nouveau nom de répertoire, puis cliquez sur **OK**.
3. Dans **app** > **src** > **main** > **res** > **raw**, créez un fichier JSON appelé `auth_config.json`, puis collez la configuration MSAL que vous avez enregistrée. Consultez [Configuration MSAL pour plus d’informations](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. Dans **app** > **src** > **main** > **AndroidManifest.xml**, ajoutez l’activité `BrowserTabActivity` ci-dessous au corps de l’application. Cette entrée permet à Microsoft de rappeler votre application une fois l’authentification terminée :

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Remplacez le nom de package que vous avez inscrit dans le portail Azure pour la valeur `android:host=`.
    Remplacez le hachage de clé que vous avez inscrit dans le portail Azure pour la valeur `android:path=`. Le hachage de signature ne doit pas être encodé en tant qu’URL.

5. Dans le fichier **AndroidManifest.xml**, juste au-dessus de la balise `<application>`, ajoutez les autorisations suivantes :

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Créer l’interface utilisateur de l’application

1. Dans la fenêtre de projet Android Studio, accédez à **app** > **src** > **main** > **res** > **layout**, ouvrez **activity_main.xml**, puis ouvrez la vue **Texte**.
2. Changez la disposition de l’activité. Par exemple, remplacez `<androidx.coordinatorlayout.widget.CoordinatorLayout` par `<androidx.coordinatorlayout.widget.DrawerLayout`. 
3. Ajoutez la propriété `android:orientation="vertical"` au nœud `LinearLayout`.
4. Collez le code suivant dans le nœud `LinearLayout`, en remplaçant le contenu actuel :

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Ajouter MSAL à votre projet

1. Dans la fenêtre de projet Android Studio, accédez à **app** > **src** > **build.gradle**.
2. Sous **Dépendances**, collez ce qui suit :

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3+'
    ```

### <a name="use-msal"></a>Utiliser MSAL

Apportez ensuite des changements à `MainActivity.java` pour ajouter et utiliser MSAL dans votre application.
Dans la fenêtre de projet Android Studio, accédez à**app** > **src** > **main** > **java** > **com.example.(votre_application)** , puis ouvrez `MainActivity.java`.

#### <a name="required-imports"></a>Importations nécessaires

Ajoutez les importations suivantes vers le haut de `MainActivity.java` :

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Instancier MSAL

Dans la classe `MainActivity`, nous devrons instancier MSAL et quelques configurations concernant les tâches que l’app effectuera, y compris les étendues et l’API web à laquelle nous voulez accéder.

Copiez les variables suivantes dans la classe `MainActivity` :

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Remplacez le contenu de `onCreate()` par le code suivant pour instancier MSAL :

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Le code ci-dessus tente de connecter les utilisateurs en mode sans assistance quand ils ouvrent votre application via `getAccounts()` et, en cas de succès, `acquireTokenSilentAsync()`.  Dans les sections suivantes, nous allons implémenter le gestionnaire de rappel pour le cas où il n’existe aucun compte connecté.

#### <a name="use-msal-to-get-tokens"></a>Utiliser la bibliothèque MSAL pour obtenir des jetons

Nous pouvons maintenant implémenter la logique de traitement de l’interface utilisateur de l’application et obtenir des jetons de manière interactive via MSAL.

MSAL propose deux méthodes principales pour obtenir des jetons : `acquireTokenSilentAsync()` et `acquireToken()`.  

`acquireTokenSilentAsync()` connecte un utilisateur et obtenir des jetons sans aucune intervention de l’utilisateur si un compte est présent. Si l’opération réussit, MSAL transmet les jetons à votre application ; le cas contraire, une exception `MsalUiRequiredException` est générée.  Si cette exception est générée, ou si vous souhaitez que l’utilisateur ait une expérience de connexion interactive (informations d’identification, MFA ou autres stratégies d’accès conditionnel nécessaires ou non), utilisez `acquireToken()`.  

`acquireToken()` affiche l’IU durant la tentative de connexion de l’utilisateur et l’obtention de jetons. Toutefois, il peut utiliser des cookies de session dans le navigateur ou un compte dans Microsoft Authenticator pour fournir une expérience d’authentification unique (SSO) interactive.

Créez les trois méthodes d’IU suivantes dans la classe `MainActivity` :

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Ajoutez les méthodes suivantes pour obtenir l’activité actuelle et traiter les rappels en modes sans assistance et interactif :

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Utiliser MSAL pour se déconnecter

Ajoutez ensuite la prise en charge de la déconnexion.

> [!Important]
> La déconnexion de MSAL entraîne la suppression de toutes les informations connues sur un utilisateur dans l’application. Toutefois, l’utilisateur a toujours une session active sur son appareil. Si l’utilisateur tente de se reconnecter, il peut éventuellement voir l’IU de connexion, mais il n’est pas obligé de ressaisir ses informations d’identification, car la session sur l’appareil est toujours active.

Pour ajouter une fonctionnalité de déconnexion, ajoutez la méthode suivante dans la classe `MainActivity`. Cette méthode parcourt tous les comptes et les supprime :

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Appeler l’API Microsoft Graph

Une fois que nous avons reçu un jeton, nous pouvons adresser une requête à l’[API Microsoft Graph](https://graph.microsoft.com). Le jeton d’accès est situé dans `AuthenticationResult` à l’intérieur de la méthode `onSuccess()` du rappel d’authentification. Pour construire une demande autorisée, votre application devra ajouter le jeton d’accès à l’en-tête HTTP :

| clé d’en-tête    | value                 |
| ------------- | --------------------- |
| Authorization | Porteur \<jeton-accès> |

Ajoutez les deux méthodes suivantes dans la classe `MainActivity` pour appeler l’API Graph et mettre à jour l’IU :

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Applications multicompte

Cette application est adaptée à un scénario de compte unique. MSAL prend également en charge les scénarios multicomptes. Toutefois, cela nécessite du travail supplémentaire de la part des applications. Vous devrez créer l’interface utilisateur pour aider l’utilisateur à sélectionner le compte qu’il souhaite utiliser pour chaque action nécessitant des jetons. Sinon, votre application peut implémenter une approche heuristique pour sélectionner le compte à utiliser via la méthode `getAccounts()`.

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Générez et déployez l’application sur un appareil de test ou un émulateur. Vous devez pouvoir vous connecter et obtenir des jetons pour les comptes Azure AD ou les comptes personnels Microsoft.

Une fois que vous êtes connecté, l’application affiche les données retournées par le point de terminaison `/me` Microsoft Graph.

### <a name="consent"></a>Consentement

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées.  Bien que la plupart des utilisateurs puissent donner leur accord, certains locataires Azure AD ont désactivé le consentement de l’utilisateur, ce qui oblige les administrateurs à donner leur consentement au nom de tous les utilisateurs. Pour permettre la prise en charge de ce scénario, inscrivez les étendues de votre application sur le portail Azure.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez l’objet d’application que vous avez créé à l’étape [Inscrivez votre application](#register-your-application).

## <a name="get-help"></a>Obtenir de l’aide

Si vous rencontrez des problèmes avec ce tutoriel ou avec la plateforme d’identités Microsoft, consultez le [Centre d’aide et de support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Aidez-nous à améliorer la plateforme d’identité Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
