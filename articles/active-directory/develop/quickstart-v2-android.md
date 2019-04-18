---
title: Guide de démarrage rapide Android pour la plateforme d’identités Microsoft | Azure
description: Découvrez comment les applications Android peuvent appeler une API qui nécessite des jetons d’accès pour le point de terminaison de la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495309"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Démarrage rapide : Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ce démarrage rapide contient un exemple de code qui montre comment une application Android peut connecter des comptes personnels ou professionnels et scolaires, obtenir un jeton d’accès et appeler l’API Microsoft Graph.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Prérequis**
> * Android Studio 3+
> * Android 21+ est requis 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement vos application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application :
> 1. Accédez au nouveau volet [Portail Azure - Inscriptions des applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>      - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `Android-Quickstart`.
>      - Appuyez sur le bouton `Register`.
> 1. Accédez à `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients`, puis sélectionnez l’URI de redirection au format **msal{AppId}://auth**. Enregistrez la modification.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Étape 1 : Configuration de votre application
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter une URL de réponse telle que **msal{AppId}://auth** (où {AppId} correspond à l’ID de votre application).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-android/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

* [Télécharger le projet Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Étape 3 : Configurer votre projet

> [!div renderon="docs"]
> Si vous avez sélectionné l’option 1 ci-dessus, vous pouvez ignorer ces étapes. Ouvrez le projet dans Android Studio et exécutez l’application. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrayez et ouvrez le projet dans Android Studio.
> 1. Dans **app** > **res** > **raw**, ouvrez **auth_config.json**.
> 1. Modifiez **auth_config.json** et remplacez les `client_id` et `tenant_id` :
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Dans **app** > **manifests**, ouvrez a**AndroidManifest.xml**.
> 1. Ajoutez l’activité suivante au nœud **manifest\application**. Ce code permet à Microsoft d’effectuer un rappel en direction de votre application :   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Extrayez et ouvrez le projet dans Android Studio.
> 1. Dans **app** > **res** > **raw**, ouvrez **auth_config.json**.
> 1. Modifiez **auth_config.json** et remplacez les `client_id` et `redirect_uri` :
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Dans **app** > **manifests**, ouvrez a**AndroidManifest.xml**.
> 1. Ajoutez l’activité suivante au nœud **manifest\application**. Cet extrait de code inscrit une activité **BrowserTabActivity** pour autoriser le système d’exploitation à reprendre l’exécution de votre application une fois l’authentification effectuée :
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Remplacez `<ENTER_THE_APPLICATION_ID_HERE>` par l’*ID d’application* de votre application. Si vous ne connaissez pas l’*ID d’application*, accédez à la page *Vue d’ensemble*.

## <a name="more-information"></a>Informations complémentaires

Lisez les sections suivantes pour plus d’informations sur ce démarrage rapide.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Vous pouvez utiliser Gradle pour l’installer en ajoutant le code suivant dans **Scripts Gradle** > **build.gradle (Module : app)** sous **Dépendances** :

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```java
import com.microsoft.identity.client.*;
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Où : ||
> |---------|---------|
> |`R.raw.auth_config` | Ce fichier contient les configurations pour votre application, y compris l’ID de votre application et du client, les utilisateurs connectés et plusieurs autres options de personnalisation. |

### <a name="requesting-tokens"></a>Demande de jetons

MSAL utilise deux méthodes pour acquérir des jetons : `acquireToken` et `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Obtention d’un jeton d’utilisateur de manière interactive

Certaines situations exigent de forcer les utilisateurs à interagir avec le point de terminaison de la plateforme d’identités Microsoft, ce qui entraîne un changement de contexte sur le navigateur système pour valider les informations d’identification des utilisateurs ou pour le consentement. Voici quelques exemples :

* La première connexion des utilisateurs à l’application
* Quand les utilisateurs doivent de nouveau entrer leurs informations d’identification, car le mot de passe a expiré
* Lorsque votre application demande l’accès à une ressource pour laquelle l’utilisateur doit donner son consentement
* Lorsqu’une authentification à 2 facteurs est requise

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Où :||
> |---------|---------|
> | `SCOPES` | Contient les étendues demandées (c’est-à-dire `{ "user.read" }` pour Microsoft Graph ou `{ "<Application ID URL>/scope" }` pour les API web personnalisées (par exemple, `api://<Application ID>/access_as_user`)) |
> | `getAuthInteractiveCallback` | Rappel exécuté lorsque le contrôle est renvoyé à l’application après l’authentification |

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux

Vous ne voulez pas obliger l’utilisateur à valider ses informations d’identification chaque fois qu’il doit accéder à une ressource. La plupart du temps, vous souhaitez que les acquisitions et renouvellements de jetons se fassent sans aucune interaction de l’utilisateur. Vous pouvez utiliser la méthode `AcquireTokenSilentAsync` pour obtenir des jetons pour accéder aux ressources protégées après la méthode `acquireToken` initiale :

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Où :||
> |---------|---------|
> | `SCOPES` | Contient les étendues demandées (c’est-à-dire `{ "user.read" }` pour Microsoft Graph ou `{ "<Application ID URL>/scope" }` pour les API web personnalisées (par exemple, `api://<Application ID>/access_as_user`)) |
> | `accounts.get(0)` | Contient le compte pour lequel vous tentez d’obtenir des jetons en mode silencieux |
> | `getAuthInteractiveCallback` | Rappel exécuté lorsque le contrôle est renvoyé à l’application après l’authentification |

## <a name="next-steps"></a>Étapes suivantes

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Découvrez les étapes permettant de créer l’application utilisée dans ce démarrage rapide

Essayez le didacticiel Android pour apprendre à créer, étape par étape, des applications et des fonctionnalités, et pour obtenir une explication complète de ce démarrage rapide.

> [!div class="nextstepaction"]
> [Tutoriel d’appel de l’API Graph Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki de la bibliothèque MSAL pour Android

En savoir plus sur la bibliothèque MSAL pour Android :

> [!div class="nextstepaction"]
> [Wiki de la bibliothèque MSAL pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
