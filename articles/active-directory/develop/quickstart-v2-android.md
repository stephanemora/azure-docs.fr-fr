---
title: Guide de démarrage rapide Android pour la plateforme d’identités Microsoft | Azure
description: Découvrez comment les applications Android peuvent appeler une API qui nécessite des jetons d’accès pour le point de terminaison de la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678051"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Démarrage rapide : Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application Android

Ce guide de démarrage rapide utilise un exemple de code pour montrer comment une application Android peut connecter des comptes personnels, professionnels ou scolaires, puis obtenir un jeton d’accès et appeler l’API Microsoft Graph.

![Capture d’écran de l’exemple d’application](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Composants requis**
> * Android Studio 
> * Le logiciel Android 16+ est requis

## <a name="step-1-get-the-sample-app"></a>Étape 1 : Obtenir l’exemple d’application

[Clonez le code](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Étape 2 : Inscrivez votre application

Pour inscrire un objet d’application et ajouter manuellement les informations d’inscription de cet objet à l’exemple de projet, effectuez les étapes suivantes :

1. Accédez au [portail Azure](https://aka.ms/MobileAppReg).
1. Ouvrez le panneau [Inscriptions d’applications](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps), puis cliquez sur **+Nouvelle inscription**.
1. Entrez un **Nom** pour l’inscription de votre application, puis, sans définir d’URI de redirection, cliquez sur **Inscrire**.
1. Dans la section **Gérer**, sélectionnez **Authentification** >  **+ Ajouter une plateforme** > **Android**. (Vous devrez peut-être sélectionner **Essayer la nouvelle expérience** près du haut du panneau pour voir cet écran.)
1. Entrez le **nom du package** de votre projet, en l’occurrence `com.azuresamples.msalandroidapp`.
1. Dans la section **Hachage de signature** de la page **Configurer votre application Android**, cliquez sur **Création d’un hachage de signature de développement**, puis copiez la commande KeyTool à utiliser pour la plateforme que vous utilisez pour développer votre application Android.

   > [!Note]
   > Keytool.exe est installé en même temps que le kit de développement Java (JDK). Vous devez également installer l’outil OpenSSL pour exécuter la commande KeyTool.  Le chemin doit comporter keytool et le répertoire OpenSSL\bin.

1. Exécutez la commande keytool que vous avez copiée à partir du portail dans une fenêtre de terminal.
1. Entrez le hachage de signature généré dans le portail sous **Hachage de signature**.
1. Cliquez sur `Configure` et effectuez une copie de la **configuration MSAL**. Vous copierez et collerez celle-ci dans un fichier de configuration à l’étape suivante. Cliquez sur **Done**.

## <a name="step-3-add-your-app-registration"></a>Étape 3 : Ajouter votre inscription d’application

1. Ouvrez l’exemple de projet dans Android Studio.
1. Dans **app** > **res** > **raw**, ouvrez **auth_config_multiple_account.json**.  Collez le contenu de la configuration MSAL. Cette opération a pour effet d’ajouter l’ID client, l’ID de locataire et la valeur redirect_uri à partir du portail. Le fichier doit ressembler à ceci, mais avec les valeurs renseignées pour l’ID client, l’ID de locataire et la valeur redirect_uri :

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
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

1. Ouvrez **app** > **res** > **raw**, ouvrez **auth_config_single_account.json**, puis collez le contenu de la configuration MSAL. Le fichier doit ressembler au fichier **auth_config_multiple_account.json** ci-dessus.
1. Dans **app** > **manifests** > **AndroidManifest.xml**, recherchez l’activité `BrowserTabActivity`. Cette entrée permet à Microsoft de rappeler votre application une fois l’authentification terminée :

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Remplacez le nom de package par le nom du package que vous avez inscrit dans le portail Azure pour la valeur `android:host=`.  Dans ce cas, il s’agit de : `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > La valeur **android:path** **doit** comporter un caractère « / » de début, sinon, une ligne rouge apparaît sous la valeur et l’exemple d’application ne s’exécute pas.
     
1. Remplacez le hachage de clé que vous avez obtenu en exécutant keytool précédemment et entré dans le portail Azure, pour la valeur `android:path=`. Le hachage de signature ne doit pas être encodé en tant qu’URL.

## <a name="step-4-run-the-sample-app"></a>Étape 4 : Exécution de l'exemple d'application

Sélectionnez votre émulateur ou votre appareil à partir de la liste déroulante des **Appareils disponibles** d’Android Studio et exécutez l’application.

L’exemple d’application démarre sur l’écran **Single Account Mode** (Mode monocompte). Une étendue par défaut, **user.read**, est fournie d’office, qui est utilisée lors de la lecture de vos propres données de profil pendant l’appel de l’API Microsoft Graph. L’URL de l’appel de l’API Microsoft Graph est fournie par défaut. Vous pouvez changer ces deux éléments si vous le souhaitez.

![Exemple d’application MSAL présentant l’utilisation d’un seul compte et de plusieurs comptes](./media/quickstart-v2-android/quickstart-sample-app.png)

Utilisez le menu de l’application pour passer d’un mode de compte à l’autre.

En mode monocompte, connectez-vous à l’aide d’un compte professionnel ou familial :

1. Sélectionnez **Get graph data interactively** (Obtenir les données de graphique de manière interactive) pour inviter l’utilisateur à entrer ses informations d’identification. Vous verrez la sortie de l’appel à l’API Microsoft Graph en bas de l’écran.
2. Une fois connecté, sélectionnez **Obtenir les données du graphique en mode silencieux** pour appeler l’API Microsoft Graph sans réinviter l’utilisateur à fournir ses informations d’identification. Vous verrez la sortie de l’appel à l’API Microsoft Graph en bas de l’écran.

En mode multicompte, vous pouvez répéter les mêmes étapes.  En outre, vous pouvez supprimer le compte connecté, ce qui supprime également les jetons mis en cache pour ce compte.

## <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

Le code est organisé en fragments qui montrent comment écrire une application MSAL monocompte et multicompte. Les fichiers de code sont organisés comme suit :

| Fichier  | Illustre le  |
|---------|---------|
| MainActivity | Gère l’interface utilisateur |
| MSGraphRequestWrapper  | Appelle l’API Microsoft Graph à l’aide du jeton fourni par l’application MSAL. |
| MultipleAccountModeFragment  | Initialise une application multicompte, charge un compte d’utilisateur et obtient un jeton pour appeler l’API Microsoft Graph. |
| SingleAccountModeFragment | Initialise une application monocompte, charge un compte d’utilisateur et obtient un jeton pour appeler l’API Microsoft Graph. |
| res/auth_config_multiple_account.json  | Fichier de configuration multicompte |
| res/auth_config_single_account.json  | Fichier de configuration monocompte |
| Scripts Gradle/build.grade (Module : app) | Les dépendances de la bibliothèque MSAL sont ajoutées ici. |

Nous allons maintenant examiner ces fichiers plus en détail, notamment le code spécifique à MSAL.

### <a name="add-msal-to-the-app"></a>Ajouter MSAL à l’application

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Gradle 3.0 et plus installe la bibliothèque quand vous ajoutez le code suivant à **Scripts Gradle** > **build.gradle (Module : app)** , sous **Dépendances** :

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Vous pouvez le voir dans l’exemple de projet dans build.gradle (module : app) :

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Ce code indique à Gradle de télécharger et de générer la bibliothèque MSAL à partir de Maven central.

### <a name="msal-imports"></a>Importations pour la bibliothèque MSAL

Les importations pertinentes pour la bibliothèque MSAL sont `com.microsoft.identity.client.*`.  Par exemple, vous verrez `import com.microsoft.identity.client.PublicClientApplication;`, qui est l’espace de noms de la classe `PublicClientApplication`, qui représente votre application cliente publique.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Ce fichier montre comment créer une application MSAL monocompte et appeler une API Microsoft Graph.

Les applications monocomptes sont utilisées uniquement par un seul utilisateur.  Par exemple, vous pouvez avoir un seul compte avec lequel vous vous connectez à votre application de mappage.

#### <a name="single-account-msal-initialization"></a>Initialisation de MSAL monocompte

Dans `onCreateView()`, un compte unique `PublicClientApplication` est créé à l’aide des informations de configuration stockées dans le fichier `auth_config_single_account.json`.  Voici comment initialisez la bibliothèque MSAL pour l’utiliser dans une application MSAL monocompte :

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Connecter un utilisateur

Le code permettant de connecter un utilisateur se trouve dans `initializeUI()`, dans le gestionnaire de clic `signInButton`.

Appelez `signIn()` avant d’essayer d’acquérir des jetons. `signIn()` se comporte comme si `acquireToken()` est appelé, ce qui se traduit par une invite interactive permettant à l’utilisateur de se connecter.

La connexion d’un utilisateur est une opération asynchrone. Un rappel est passé qui appelle l’API Microsoft Graph et met à jour l’interface utilisateur une fois que l’utilisateur se connecte :

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Déconnecter un utilisateur

Le code permettant de déconnecter un utilisateur se trouve dans `initializeUI()`, dans le gestionnaire de clic `signOutButton`.  La déconnexion d’un utilisateur est une opération asynchrone. La déconnexion de l’utilisateur efface également le cache de jeton pour ce compte. Un rappel est créé pour mettre à jour l’interface utilisateur une fois que le compte d’utilisateur est déconnecté :

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Obtenir un jeton de manière interactive ou silencieuse

Pour réduire au minimum le nombre d’invites présentées à l’utilisateur, vous obtenez généralement un jeton en mode silencieux. Ensuite, en cas d’erreur, essayez d’accéder au jeton de manière interactive. La première fois que l’application appelle `signIn()`, elle agit efficacement comme un appel à `acquireToken()`, ce qui a pour effet d’inviter l’utilisateur à fournir des informations d’identification.

Voici certaines situations dans lesquelles l’utilisateur final peut être amené à sélectionner son compte, entrer ses informations d’identification ou accepter les autorisations que votre application a demandées :

* La première fois que l’utilisateur se connecte à l’application
* Si un utilisateur réinitialise son mot de passe, il doit entrer ses informations d’identification
* Si le consentement est révoqué
* Si votre application requiert un consentement explicite
* Lorsque votre application demande l'accès à une ressource pour la première fois
* Lorsque l'authentification multifacteur ou d'autres stratégies d'accès conditionnel sont requises

Le code permettant d’obtenir un jeton de manière interactive, c’est-à-dire avec l’interface utilisateur qui implique l’utilisateur, se trouve dans `initializeUI()`, dans le gestionnaire de clic `callGraphApiInteractiveButton` :

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Si l’utilisateur s’est déjà connecté, `acquireTokenSilentAsync()` permet aux applications de demander des jetons en mode silencieux comme indiqué dans `initializeUI()`, dans le gestionnaire de clic `callGraphApiSilentButton` :

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Charger un compte

Le code permettant de charger un compte se trouve dans `loadAccount()`.  Le chargement du compte de l’utilisateur étant une opération asynchrone, les rappels pour gérer le moment où le compte se charge, change ou une erreur se produit sont passés à MSAL.  Le code suivant gère également `onAccountChanged()`, qui se produit quand un compte est supprimé, que l’utilisateur change de compte, etc.

```java
private void loadAccount() {
    ...

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
```

#### <a name="call-microsoft-graph"></a>Appeler Microsoft Graph

Quand un utilisateur est connecté, l’appel à Microsoft Graph est effectué par le biais d’une requête HTTP par `callGraphAPI()`. Cette fonction est un wrapper qui simplifie l’exemple en exécutant des tâches telles que l’obtention du jeton d’accès à partir du `authenticationResult` ainsi que l’empaquetage de l’appel à MSGraphRequestWrapper et l’affichage des résultats de l’appel.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Il s’agit du fichier de configuration d’une application MSAL qui utilise un seul compte.

Consultez [Comprendre le fichier de configuration MSAL Android](msal-configuration.md) pour obtenir une explication de ces champs.

Notez la présence de `"account_mode" : "SINGLE"`, qui configure cette application pour qu’elle utilise un seul compte.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Ce fichier montre comment créer une application MSAL multicompte et appeler une API Microsoft Graph. 

Un exemple d’application multicompte est une application de messagerie qui vous permet d’utiliser plusieurs comptes d’utilisateur, tels qu’un compte professionnel et un compte personnel.

#### <a name="multiple-account-msal-initialization"></a>Initialisation de MSAL multicompte

Dans `onCreateView()`, un objet d’application multicompte (`IMultipleAccountPublicClientApplication`) est créé à l’aide des informations de configuration stockées dans le fichier `auth_config_multiple_account.json file` :

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

L’objet `MultipleAccountPublicClientApplication` créé est stocké dans une variable membre de classe afin qu’il puisse être utilisé pour interagir avec la bibliothèque MSAL afin d’obtenir des jetons et de charger et supprimer le compte d’utilisateur.

#### <a name="load-an-account"></a>Charger un compte

Les applications multicomptes appellent généralement `GetAccounts()` afin de sélectionner le compte à utiliser pour les opérations MSAL. Le code permettant de charger un compte se trouve dans `loadAccount()`.  Le chargement du compte de l’utilisateur est une opération asynchrone. Ainsi, un rappel gère les situations où le compte est chargé, change ou une erreur se produit.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Obtenir un jeton de manière interactive ou silencieuse

Voici certaines situations dans lesquelles l’utilisateur final peut être amené à sélectionner son compte, entrer ses informations d’identification ou accepter les autorisations que votre application a demandées :

* La première connexion des utilisateurs à l’application
* Si un utilisateur réinitialise son mot de passe, il doit entrer ses informations d’identification 
* Si le consentement est révoqué 
* Si votre application requiert un consentement explicite 
* Lorsque votre application demande l'accès à une ressource pour la première fois
* Lorsque l'authentification multifacteur ou d'autres stratégies d'accès conditionnel sont requises

Les applications multicomptes doivent généralement acquérir des jetons de manière interactive, c’est-à-dire avec l’interface utilisateur qui implique l’utilisateur, avec un appel à `acquireToken()`.  Le code permettant d’obtenir un jeton de manière interactive se trouve dans `initializeUI()`, dans le gestionnaire de clic `callGraphApiInteractiveButton` :

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Les applications ne doivent pas demander à l’utilisateur de se connecter chaque fois qu’elles ont besoin d’un jeton. Si l’utilisateur s’est déjà connecté, `acquireTokenSilentAsync()` permet aux applications de demander des jetons sans solliciter l’utilisateur, comme indiqué dans `initializeUI()` dans le gestionnaire de clic `callGraphApiSilentButton` :

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Supprimer un compte

Le code permettant de supprimer un compte et tous les jetons mis en cache pour le compte se trouve dans `initializeUI()` dans le gestionnaire du bouton de suppression de compte. Avant de pouvoir supprimer un compte, vous avez besoin d’un objet compte, que vous obtenez à partir de fonctions MSAL comme `getAccounts()` et `acquireToken()`. Étant donné que la suppression d’un compte est une opération asynchrone, le rappel `onRemoved` est fourni pour mettre à jour l’interface utilisateur.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Il s’agit du fichier de configuration d’une application MSAL qui utilise plusieurs comptes.

Consultez [Comprendre le fichier de configuration MSAL Android](msal-configuration.md) pour obtenir une explication de ces champs.

Contrairement au fichier de configuration [auth_config_single_account.json](#auth_config_single_accountjson), ce fichier de configuration a `"account_mode" : "MULTIPLE"` au lieu de `"account_mode" : "SINGLE"`, car il s’agit d’une application multicompte.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
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

## <a name="next-steps"></a>Étapes suivantes

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Découvrez les étapes permettant de créer l’application utilisée dans ce démarrage rapide

Essayez le didacticiel Android pour apprendre à créer, étape par étape, des applications et des fonctionnalités, et pour obtenir une explication complète de ce démarrage rapide.

> [!div class="nextstepaction"]
> [Didacticiel d’appel de l’API Graph Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki de la bibliothèque MSAL pour Android

En savoir plus sur la bibliothèque MSAL pour Android :

> [!div class="nextstepaction"]
> [Wiki de la bibliothèque MSAL pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
