---
title: Connecter et déconnecter des utilisateurs et appeler Microsoft Graph (Android) – Plateforme d’identités Microsoft | Azure
description: Obtenir un jeton d’accès et appeler Microsoft Graph ou des API qui nécessitent des jetons d’accès de la plateforme d’identités Microsoft (Android)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5c8bd5accefceee042601c3cf7d71f5e9131e04e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880820"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Tutoriel : Connecter des utilisateurs et appeler Microsoft Graph à partir d’une application Android 

>[!NOTE]
>Ce tutoriel présente des exemples simplifiés montrant comment utiliser MSAL pour Android. Pour plus de simplicité, ce tutoriel utilise uniquement le mode monocompte. Vous pouvez également consulter le dépôt et cloner l’[exemple d’application préconfiguré](https://github.com/Azure-Samples/ms-identity-android-java/) pour explorer des scénarios plus complexes. Consultez le [guide de démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) pour plus d’informations sur l’exemple d’application, la configuration et l’inscription. 

Ce tutoriel explique comment intégrer votre application Android à la plateforme d’identités Microsoft à l’aide de la bibliothèque d’authentification Microsoft pour Android. Vous allez apprendre à connecter et déconnecter un utilisateur, à obtenir un jeton d’accès pour appeler l’API Microsoft Graph, et à envoyer une demande à l’API Graph. 

> [!div class="checklist"]
> * Intégrer votre application Android à la plateforme d’identités Microsoft 
> * Connecter un utilisateur 
> * Obtenir un jeton d’accès pour appeler l’API Microsoft Graph 
> * Appeler l’API Microsoft Graph 
> * Déconnecter un utilisateur 

À la fin de ce tutoriel, votre application acceptera les connexions de comptes Microsoft personnels (notamment outlook.com, live.com et d’autres) ainsi que de comptes professionnels ou scolaires de toute entreprise ou organisation utilisant Azure Active Directory.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="how-this-tutorial-works"></a>Fonctionnement de ce tutoriel

![Fonctionnement de l’exemple d’application généré par ce tutoriel](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L’application utilisée dans ce tutoriel est destinée à connecter des utilisateurs et à obtenir des données au nom de ces derniers. Ces données sont accessibles par le biais d’une API protégée (l’API Microsoft Graph) qui nécessite une autorisation et est protégée par la plateforme d’identités Microsoft.

Plus précisément :

* Votre application connectera l’utilisateur via un navigateur ou Microsoft Authenticator et le portail d’entreprise Intune.
* L’utilisateur final acceptera les autorisations que votre application a demandées.
* Votre application émet un jeton d’accès pour l’API Microsoft Graph.
* Le jeton d’accès est inclus dans la requête HTTP adressée à l’API web.
* La réponse Microsoft Graph est traitée.

Cet exemple utilise la bibliothèque d’authentification Microsoft pour Android (MSAL) pour implémenter l’authentification : [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL renouvelle automatiquement les jetons, fournit une authentification unique (SSO) entre les autres applications de l’appareil et gère les comptes.

### <a name="prerequisites"></a>Prérequis

* Ce tutoriel nécessite Android Studio version 3.5+.

## <a name="create-a-project"></a>Créer un projet
Si vous n’avez pas encore d’application Android, effectuez les étapes suivantes pour configurer un nouveau projet. 

1. Ouvrez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio**.
2. Sélectionnez **Activité de base**, puis **Suivant**.
3. Donnez un nom à votre application.
4. Enregistrez le nom du package. Vous allez l’entrer plus tard dans le portail Azure.
5. Remplacez le langage **Kotlin** par **Java**.
6. Affectez à **Niveau d’API minimal** la valeur **API 19** ou une valeur supérieure, puis cliquez sur **Terminer**.
7. Dans la vue du projet, dans la liste déroulante, choisissez **Projet** pour afficher les fichiers projet sources et non-sources, ouvrez **app/build.gradle**, puis affectez à `targetSdkVersion` la valeur `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Intégrer à l’aide de la bibliothèque d’authentification Microsoft 

### <a name="register-your-application"></a>Inscrivez votre application

1. Accédez au [portail Azure](https://aka.ms/MobileAppReg).
2. Ouvrez le panneau [Inscriptions d’applications](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), puis cliquez sur **+Nouvelle inscription**.
3. Entrez un **nom** pour votre application, puis, **sans** définir d’URI de redirection, cliquez sur **Enregistrer**.
4. Dans la section **Gérer** du volet qui apparaît, sélectionnez **Authentification** >  **+ Ajouter une plateforme** > **Android**. (Vous devrez peut-être sélectionner « Essayer la nouvelle expérience » près du haut du panneau pour voir cette section.)
5. Entrez le nom du package de votre projet. Si vous avez téléchargé le code, cette valeur est `com.azuresamples.msalandroidapp`.
6. Dans la section **Hachage de signature** de la page **Configurer votre application Android**, cliquez sur **Création d’un hachage de signature de développement** et copiez la commande KeyTool à utiliser pour votre plateforme.

   > [!Note]
   > Keytool.exe est installé en même temps que le kit de développement Java (JDK). Vous devez également installer l’outil OpenSSL pour exécuter la commande KeyTool. Pour plus d’informations, reportez-vous à la [documentation Android sur la génération d’une clé](https://developer.android.com/studio/publish/app-signing#generate-key). 

7. Entrez le **Hachage de signature** généré par KeyTool.
8. Cliquez sur `Configure` et enregistrez la **Configuration MSAL** qui apparaît dans la page **Configuration Android** pour pouvoir l’entrer plus tard quand vous devrez configurer votre application.  Cliquez sur **Done**.

### <a name="configure-your-application"></a>Configuration de votre application 

1. Dans le volet de projet d’Android Studio, accédez à **app\src\main\res**.
2. Cliquez avec le bouton droit sur **res**, puis choisissez **Nouveau** > **Répertoire**. Entrez `raw` en tant que nouveau nom de répertoire, puis cliquez sur **OK**.
3. Dans **app** > **src** > **main** > **res** > **raw**, créez un fichier JSON appelé `auth_configbn_single_account.json`, puis collez la configuration MSAL que vous avez enregistrée. 

    Sous l’URI de redirection, collez : 
    ```json
      "account_mode" : "SINGLE",
    ```
    Votre fichier de configuration doit ressembler à l’exemple suivant : 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >Ce tutoriel montre uniquement comment configurer une application en mode monocompte. Consultez la documentation pour plus d’informations sur le [mode monocompte ou multicompte](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) et la [configuration de votre application](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration).
   
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
    Remplacez le hachage de clé que vous avez inscrit dans le portail Azure pour la valeur `android:path=`. Le hachage de signature **ne doit pas** être encodé en tant qu’URL. Assurez-vous de la présence d’un caractère `/` de début dans votre hachage de signature. 
    >[!NOTE]
    >Le « nom du package » avec lequel vous remplacerez la valeur `android:host` doit ressembler à ce qui suit : « com.azuresamples.msalandroidapp ». Le « hachage de signature » avec lequel vous remplacerez votre valeur `android:path` doit ressembler à : « /1wIqXSqBj7w+h11ZifsnqwgyKrY= ». Vous pourrez également trouver ces valeurs dans le panneau Authentification de l’inscription de votre application. Notez que votre URI de redirection ressemblera à : « msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D ». Le hachage de signature est encodé avec une URL à la fin de cette valeur, mais le hachage de signature **ne doit pas** être encodé avec une URL dans votre valeur `android:path`. 

## <a name="use-msal"></a>Utiliser MSAL 

### <a name="add-msal-to-your-project"></a>Ajouter MSAL à votre projet

1. Dans la fenêtre de projet Android Studio, accédez à **app** > **src** > **build.gradle** et ajoutez : 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Informations complémentaires sur le kit SDK Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Importations nécessaires 

Ajoutez le code suivant au début de **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Instancier PublicClientApplication
#### <a name="initialize-variables"></a>Initialiser les variables 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Dans la classe `MainActivity`, reportez-vous à la méthode onCreate() suivante pour instancier MSAL à l’aide de `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Soyez à l’écoute des boutons et appelez les méthodes ou consignez les erreurs en conséquence. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> La déconnexion de MSAL entraîne la suppression de toutes les informations connues sur un utilisateur dans l’application. Toutefois, l’utilisateur a toujours une session active sur son appareil. Si l’utilisateur tente de se reconnecter, il peut éventuellement voir l’IU de connexion, mais il n’est pas obligé de ressaisir ses informations d’identification, car la session sur l’appareil est toujours active. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Rappel utilisé pour les demandes interactives

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Rappel utilisé pour les demandes silencieuses 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Appeler l’API Microsoft Graph 

Le code suivant montre comment appeler l’API Graph à l’aide du kit SDK Graph. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Ajouter une interface utilisateur
### <a name="activity"></a>Activité 
Si vous souhaitez modéliser votre interface utilisateur en dehors de ce tutoriel, les méthodes suivantes fournissent un guide pour la mise à jour du texte et l’écoute des boutons.

#### <a name="updateui"></a>updateUI
Activez/désactivez les boutons en fonction de l’état de connexion et du texte défini.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Méthode permettant de mettre à jour le texte dans l’interface utilisateur pour refléter la déconnexion. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Disposition 

Exemple de fichier `activity_main.xml` permettant d’afficher des boutons et des zones de texte. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Test de l'application

### <a name="run-locally"></a>Exécution locale

Générez et déployez l’application sur un appareil de test ou un émulateur. Vous devez pouvoir vous connecter et obtenir des jetons pour les comptes Azure AD ou les comptes personnels Microsoft.

Une fois que vous êtes connecté, l’application affiche les données retournées par le point de terminaison `/me` Microsoft Graph.

### <a name="consent"></a>Consentement

La première fois qu’un utilisateur se connecte à votre application, Microsoft Identity l’invitera à accepter les autorisations demandées. Certains locataires Azure AD ont désactivé le consentement de l’utilisateur, ce qui oblige les administrateurs à donner leur consentement au nom de tous les utilisateurs. Pour prendre en charge ce scénario, vous devez créer votre propre locataire ou recevoir le consentement de l’administrateur. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez l’objet d’application que vous avez créé à l’étape [Inscrivez votre application](#register-your-application).

## <a name="get-help"></a>Obtenir de l’aide

Si vous rencontrez des problèmes avec ce tutoriel ou avec la plateforme d’identités Microsoft, consultez le [Centre d’aide et de support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
