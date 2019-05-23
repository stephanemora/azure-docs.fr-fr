---
title: Prise en main d’Android - Plateforme d’identité Microsoft | Azure
description: Cet article explique comment une application Android peut obtenir un jeton d’accès et appeler une ou plusieurs API Microsoft Graph qui nécessitent des jetons d’accès à partir de la plateforme d’identité Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962150"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application Android

Dans ce tutoriel, vous allez apprendre à intégrer une application Android à la plateforme d’identité Microsoft. Plus précisément, votre application doit connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et adresser une requête à l’API Microsoft Graph.  

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Fonctionnement de ce tutoriel

![Fonctionnement de l’exemple d’application généré par ce tutoriel](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L’application utilisée dans cet exemple est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers.  Ces données seront accessibles via une API protégée (API Microsoft Graph dans ce cas) qui nécessite une autorisation.

Plus précisément :

* Votre application connectera l’utilisateur via un navigateur ou Microsoft Authenticator et le portail d’entreprise Intune.
* L’utilisateur final acceptera les autorisations que votre application a demandées. 
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft pour Android (MSAL) afin d’implémenter Auth. MSAL renouvelle automatiquement les jetons, assure l’authentification unique entre les autres applications sur l’appareil, et gère les comptes.

## <a name="prerequisites"></a>Prérequis

* Ce guide de configuration utilise Android Studio.
* L’utilisation d’Android 16 ou d’une version ultérieure est requise (version 19 ou ultérieure recommandée).

## <a name="library"></a>Bibliothèque

Ce guide utilise la bibliothèque d’authentification suivante :

|Bibliothèque|Description|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Bibliothèque d’authentification Microsoft (MSAL)|

## <a name="set-up-your-project"></a>Configuration de votre projet

Ce tutoriel va créer un projet. Si vous souhaitez plutôt télécharger le tutoriel complet, [téléchargez le code](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Création d'un projet

1. Ouvrez Android Studio, puis sélectionnez **Start a new Android Studio project** (Démarrer un nouveau projet Android Studio).
    - Si Android Studio est déjà ouvert, sélectionnez **File** (Fichier)  >  **New** (Nouveau) >  **New Project** (Nouveau projet).
2. Laissez **Empty Activity** (Activité vide) tel quel, puis sélectionnez **Next** (Suivant).
3. Nommez votre application, définissez `Minimum API level` sur **API 19 ou version ultérieure**, puis appuyez sur **Finish** (Terminer).
5. Dans votre `app/build.gradle`, définissez `targetedSdkVersion` sur 27. 

## <a name="register-your-application"></a>Inscrivez votre application

Vous pouvez inscrire votre application de deux manières, comme décrit dans les deux sections suivantes.

### <a name="register-your-app"></a>Inscrire votre application

1. Accédez au [portail Azure](https://aka.ms/MobileAppReg) > sélectionnez `New registration`. 
2. Entrez le **nom** de votre application > `Register`. **Ne définissez aucune URI de redirection à ce stade**. 
3. Dans la section `Manage`, accédez à `Authentication` > `Add a platform` > `Android`
    - Entrez le nom du package de votre projet. Si vous avez téléchargé le code, cette valeur est `com.azuresamples.msalandroidapp`. 
    - Entrez le hachage de signature de votre débogage/développement. Utilisez la commande KeyTool du portail pour générer le hachage de signature. 
4. Appuyez sur `Configure` et stockez la ***configuration MSAL*** pour une utilisation ultérieure. 

## <a name="build-your-app"></a>Générer votre application

### <a name="configure-your-android-app"></a>Configurer votre application Android

1. Cliquer avec le bouton droit sur **res** > **New** (Nouveau)  >  **Folder** (Dossier)  >  **Raw Resources Folder** (Dossier de ressources brutes)
2. Dans **app** > **res** > **raw**, créez un fichier JSON nommé `auth_config.json` puis collez votre ***configuration MSAL***. Consultez [Configuration MSAL pour plus d’informations](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. Dans **app** > **manifests** > **AndroidManifest.xml**, ajoutez l’activité `BrowserTabActivity` ci-dessous. Cette entrée permet à Microsoft de rappeler votre application une fois l’authentification terminée :

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

    Notez que le hachage de signature utilisé ne doit pas être encodé en URL dans le fichier **AndroidManifest.xml**. 

4. Dans le fichier **AndroidManifest.xml**, juste au-dessus de la balise `<application>`, ajoutez les autorisations suivantes :

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. Dans `BrowserTabActivity`, remplacez ***Package Name*** (Nom du package) et ***Signature Hash*** (Signature de hachage) par les valeurs enregistrées dans le portail Azure.

### <a name="create-the-apps-ui"></a>Créer l’interface utilisateur de l’application

1. Accédez à **res** > **layout**, puis ouvrez **activity_main.xml**.
2. Modifiez la disposition de l’activité `android.support.constraint.ConstraintLayout` ou autre pour `LinearLayout`.
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

1. Dans Android Studio, sélectionnez **Scripts Gradle** > **build.gradle (Module : application)**.
2. Sous **Dépendances**, collez le code suivant :

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Utiliser MSAL 

Les sections suivantes apportera des modifications à `MainAcitivty.java`. Nous allons parcourir chaque étape nécessaire pour ajouter et utiliser MSAL dans votre application.

#### <a name="required-imports"></a>Importations nécessaires

Ajoutez les importations suivantes à votre projet : 

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

#### <a name="instantiating-msal"></a>Instanciation de MSAL 

Dans la classe `MainActivity`, nous devrons instancier MSAL et quelques configurations concernant les tâches que l’app effectuera, y compris les étendues et l’API web à laquelle nous voulez accéder. 

Copiez les variables suivantes dans `MainActivity` :

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

Maintenant, pour instancier MSAL, copiez le code suivant à l’intérieur de la méthode `onCreate(...)` :

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

Le bloc de code ci-dessus tente de connecter des utilisateurs en mode silencieux lorsqu’ils ouvrent votre application via `getAccounts(...)` et, en cas de réussite, `acquireTokenSilentAsync(...)`.  Dans les sections suivantes, nous allons implémenter le gestionnaire de rappel pour le cas où il n’existe aucun compte connecté. 

#### <a name="use-msal-to-get-tokens"></a>Utiliser la bibliothèque MSAL pour obtenir des jetons

Nous pouvons maintenant implémenter la logique de traitement de l’interface utilisateur de l’application et obtenir des jetons de manière interactive via MSAL. 

MSAL propose deux méthodes principales pour obtenir des jetons : `acquireTokenSilentAsync` et `acquireToken`.  

`acquireTokenSilentAsync` connecte un utilisateur et obtenir des jetons sans aucune intervention de l’utilisateur si un compte est présent. Si l’opération réussit, MSAL transmet les jetons à votre application ; le cas contraire, une exception `MsalUiRequiredException` est générée.  Si cette exception est générée ou si vous souhaitez offrir à votre utilisateur une expérience de connexion interactive (saisie d’identifiants, MFA ou autres stratégies d’accès conditionnel requises ou non), vous pouvez alors utiliser `acquireToken`.  

`acquireToken` affiche toujours l’interface utilisateur lorsque vous tentez de connecter l’utilisateur et d’obtenir des jetons ; toutefois, le processus peut utiliser les cookies de session du navigateur ou un compte dans Microsoft Authenticator pour offrir une expérience interactive d’authentification unique. 

Pour commencer, créez les trois méthodes d’interface utilisateur suivantes dans la classe `MainActivity` :

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

Ajoutez ensuite une méthode pour obtenir l’activité en cours et traiter les rappels en mode silencieux et interactifs :

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

Nous allons ensuite ajouter à notre application la prise en charge de la déconnexion. 

Il est important de noter que la déconnexion avec MSAL supprime de cette application toutes les informations connues relatives à un utilisateur, mais ce dernier gardera toujours une session active sur son appareil. Si l’utilisateur tente de se reconnecter, il notera une interaction mais n’aura pas à entrer à nouveau ses identifiants car la session est active sur l’appareil. 

Pour ajouter une déconnexion, copiez la méthode suivante dans votre application, qui parcourt tous les comptes et les supprime :

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

Après avoir obtenu avec succès un jeton, nous pouvons soumettre une demande à l’API Microsoft Graph. Le jeton d’accès se trouvera dans `AuthenticationResult`, à l’intérieur de la méthode `onSuccess(...)` de rappel d’authentification. Pour construire une demande autorisée, votre application devra ajouter le jeton d’accès à l’en-tête HTTP :

| clé d’en-tête    | value                 |
| ------------- | --------------------- |
| Authorization | Porteur <jeton-accès> |

Pour effectuer cette opération dans le code, ajoutez les deux méthodes suivantes à votre application afin d’afficher le graphique des appels et de mettre à jour l’interface utilisateur : 

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

En savoir plus sur l’[API Graph Microsoft](https://graph.microsoft.com).

#### <a name="multi-account-applications"></a>Applications multicompte

Cette application est adaptée à un scénario de compte unique. MSAL prend également en charge les scénarios multicompte, mais nécessite un travail supplémentaire de la part des applications. Vous devrez créer l’interface utilisateur pour aider l’utilisateur à sélectionner le compte qu’il souhaite utiliser pour chaque action nécessitant des jetons. Sinon, votre application peut implémenter une approche heuristique pour sélectionner le compte à utiliser via la méthode `getAccounts(...)`. 

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Si vous avez suivi le code ci-dessus, essayez de générer et de déployer l’application sur un appareil de test ou un émulateur. Vous devriez pouvoir vous connecter et obtenir des jetons pour Azure AD ou des comptes personnels Microsoft. Une fois l’utilisateur connecté, cette application affichera les données retournées par le point de terminaison Microsoft Graph `/me`. 

Si vous rencontrez des problèmes, n’hésitez pas à ouvrir un problème sur ce document ou dans la bibliothèque MSAL, et faites-nous part de vos commentaires. 

### <a name="consent-to-your-app"></a>Consentement à votre application

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées.  Même si la plupart des utilisateurs peuvent donner leur accord, certains clients Azure AD ont désactivé le consentement de l’utilisateur, obligeant les administrateurs à donnent leur consentement pour le compte de tous les utilisateurs.  Pour prendre en charge ce scénario, veillez à enregistrer les étendues de votre application dans le portail Azure.

## <a name="help-and-support"></a>Aide et support

Avez-vous rencontré des problèmes avec ce tutoriel ou avec la plateforme d’identité Microsoft ? Consultez [Aide et support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
