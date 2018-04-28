---
title: Prise en main d’Azure AD Android | Microsoft Docs
description: Cet article explique comment créer une application Android qui s’intègre à Azure AD pour la connexion et appelle des API protégées par Azure AD avec OAuth2.0.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Bien démarrer avec Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Si vous développez une application de bureau, Azure Active Directory (Azure AD) facilite l’authentification de vos utilisateurs en utilisant leurs comptes Active Directory locaux. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les clients Android qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL). Cette bibliothèque a pour seule fonction de simplifier l’obtention des jetons d’accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste de tâches Android qui effectue les actions suivantes :

* obtention de jetons d’accès pour appeler l’API To-Do List à l’aide du [protocole d’authentification OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ;
* obtention de la liste des tâches d’un utilisateur ;
* déconnexion des utilisateurs.

Pour commencer, vous avez besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Étape 1 : Téléchargement et exécution de l’exemple de serveur TODO d’API REST Node.js
L’exemple TODO d’API REST Node.js est écrit spécifiquement pour fonctionner avec notre exemple existant pour la création d’une API REST To-Do à client unique pour Azure AD. Il s’agit d’un composant requis pour le démarrage rapide.

Pour plus d’informations sur la configuration associée, consultez nos exemples existants dans [Exemple de service d’API REST Azure Active Directory pour Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Étape 2 : Inscription de votre API web auprès de votre client Azure AD
Active Directory prend en charge l’ajout de deux types d’application :

- les API web qui offrent des services aux utilisateurs ;
- les applications (exécutées sur le web ou sur un appareil) qui accèdent à ces API web

Au cours de cette étape, vous allez inscrire l’API web que vous exécutez localement pour tester cet exemple. Normalement, cette API web est un service REST qui offre les fonctionnalités auxquelles vous souhaitez qu’une application ait accès. Azure AD peut contribuer à protéger n’importe quel point de terminaison.

Nous partons du principe que vous inscrivez l’API REST TODO mentionnée précédemment. Cependant, cela fonctionne pour toute API web que vous voulez protéger à l’aide d’Azure Active Directory.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Tous les services** dans le volet de gauche, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Entrez un nom convivial pour l’application (par exemple, **TodoListService**), sélectionnez **Application Web et/ou API Web**, puis cliquez sur **Suivant**.
6. Pour l’URL d’authentification, entrez l’URL de base pour l’exemple (`https://localhost:8080` par défaut).
7. Cliquez sur **OK** pour terminer l’inscription.
8. Toujours dans le portail Azure, accédez à la page de votre application, recherchez la valeur de l’ID d’application et copiez-la. Vous en aurez besoin plus tard lors de la configuration de votre application.
9. À partir de la page **Paramètres** -> **Propriétés**, mettez à jour l’URI ID d’application : entrez `https://<your_tenant_name>/TodoListService`. Remplacez `<your_tenant_name>` par le nom de votre client Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Étape 3 : Inscription de l’exemple d’application cliente native Android
Vous devez inscrire votre application web dans cet exemple. Cela permet à votre application de communiquer avec l’API web qui vient d’être inscrite. Azure AD n’autorisera même pas votre application à demander une connexion si elle n’est pas inscrite. Cela fait partie de la sécurité du modèle.

Nous partons du principe que vous inscrivez l’exemple d’application mentionné précédemment. Cependant, cette procédure fonctionne pour n’importe quelle application que vous développez.

> [!NOTE]
> Vous vous demandez peut-être pourquoi nous incorporons à la fois une application et une API web dans un seul client. Comme vous l’avez peut-être deviné, vous pouvez générer une application qui accède à une API externe inscrite dans Azure AD à partir d’un autre client. Si vous procédez ainsi, vos clients sont invités à donner leur autorisation pour l’utilisation de l’API dans l’application. La bibliothèque d’authentification Active Directory pour iOS s’occupe de cette autorisation pour vous. À mesure que nous explorerons des fonctionnalités plus avancées, vous verrez qu’il s’agit d’une partie importante du travail nécessaire pour accéder à la suite d’API Microsoft Azure et Office, ainsi qu’à tout autre fournisseur de services. Pour l’instant, comme vous avez inscrit votre API web et l’application sous le même client, vous ne voyez aucune invite de consentement. Cela est généralement le cas si vous développez une application destinée uniquement à votre entreprise.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Tous les services** dans le volet de gauche, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Entrez un nom convivial pour l’application (par exemple **TodoListClient-Android**), sélectionnez **Application cliente native**, puis cliquez sur **Suivant**.
6. Pour l’URI de redirection, entrez `http://TodoListClient`. Cliquez sur **Terminer**.
7. Dans la page de l’application, recherchez la valeur de l’ID d’application et copiez-la. Vous en aurez besoin plus tard lors de la configuration de votre application.
8. Dans la page **Paramètres**, sélectionnez **Autorisations requises**, puis **Ajouter**.  Recherchez et sélectionnez TodoListService, ajoutez l’autorisation d’**accès à TodoListService** sous **Autorisations déléguées**, puis cliquez sur **Terminé**.

Pour générer l’application avec Maven, vous pouvez utiliser pom.xml au niveau supérieur :

1. Clonez ce référentiel dans le répertoire de votre choix :

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. Suivez les étapes de la [section sur la configuration requise pour configurer votre environnement Maven pour Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven).
3. Installez l’émulateur avec le Kit de développement logiciel (SDK) 19.
4. Accédez au dossier racine où vous avez cloné le référentiel.
5. Exécutez cette commande : `mvn clean install`.
6. Remplacez le répertoire par celui de l’exemple de démarrage rapide : `cd samples\hello`.
7. Exécutez cette commande : `mvn android:deploy android:run`.

   L’application doit normalement démarrer.
8. Entrez des informations d’identification utilisateur de test pour faire un essai.

Des packages JAR sont envoyés en plus du package AAR.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Étape 4 : Téléchargement de la bibliothèque ADAL Android et ajout de cette dernière à votre espace de travail Eclipse
Nous avons fait en sorte que vous puissiez facilement disposer de plusieurs options pour utiliser la bibliothèque ADAL dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque dans Eclipse et la lier à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format de package AAR et référencer les fichiers binaires.

### <a name="option-1-source-zip"></a>Option 1 : code source dans un fichier ZIP
Pour télécharger une copie du code source, cliquez sur **Télécharger ZIP** sur le côté droit de la page. Vous pouvez également [effectuer le téléchargement à partir de GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases).

### <a name="option-2-source-via-git"></a>Option 2:  code Source via Git
Pour obtenir le code source du Kit SDK via Git, tapez :

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Option 3: fichiers binaires via Gradle
Vous pouvez obtenir les fichiers binaires à partir du référentiel central Maven. Le package AAR peut être inclus dans votre projet dans Android Studio comme suit :

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Option 4: AAR via Maven
Si vous utilisez le plug-in M2Eclipse, vous pouvez spécifier la dépendance dans votre fichier pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Option 5 : package JAR à l’intérieur du dossier de bibliothèques
Vous pouvez obtenir le fichier JAR à partir du référentiel Maven et le déposer dans le dossier **libs** de votre projet. Vous devez également copier les ressources nécessaires à votre projet, car les packages JAR ne les incluent pas.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Étape 5 : ajout des références à la bibliothèque ADAL Android dans votre projet
1. Ajoutez une référence à votre projet et spécifiez qu’il s’agit d’une bibliothèque Android. Si vous n’êtes pas sûr de la procédure à suivre, vous trouverez plus d’informations sur le [site Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Ajoutez la dépendance de projet pour le débogage dans vos paramètres de projet.
3. Mettez à jour le fichier AndroidManifest.xml du projet pour y inclure le code suivant :

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Créez une instance d’AuthenticationContext pour votre activité principale. Les détails de cet appel dépassent la cadre de cette rubrique, mais pour commencer, vous pouvez examiner l’[exemple de client natif Android](https://github.com/AzureAD/azure-activedirectory-library-for-android). Dans l’exemple suivant, SharedPreferences est le cache par défaut et l’autorité prend la forme `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` :

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Copiez ce bloc de code pour gérer la fin d’AuthenticationActivity une fois que l’utilisateur a entré les informations d’identification et reçu le code d’autorisation :

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Pour demander un jeton, définissez un rappel :

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Enfin, demandez un jeton à l’aide de ce rappel :

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Voici une explication des paramètres :

* *resource* est obligatoire et désigne la ressource à laquelle vous tentez d’accéder.
* *clientid* est obligatoire et provient d’Azure AD.
* *RedirectUri* n’est pas obligatoire pour l’appel à acquireToken. Vous pouvez définir ce paramètre sur le nom de votre package.
* *PromptBehavior* permet de demander des informations d’identification pour ignorer le cache et le cookie.
* *callback* est appelé une fois que le code d’autorisation a été échangé contre un jeton. Ce paramètre présente un objet AuthenticationResult avec jeton d’accès, date d’expiration et informations de jeton d’ID.
* *acquireTokenSilent* est facultatif. Vous pouvez appeler ce paramètre pour gérer la mise en cache et l’actualisation du jeton. Il fournit également la version de synchronisation. Il accepte *userId* comme paramètre.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Cette procédure pas à pas devrait vous permettre d’effectuer correctement l’intégration avec Azure Active Directory. Pour d’autres exemples, consultez le référentiel AzureADSamples/ sur GitHub.

## <a name="important-information"></a>Informations importantes

### <a name="broker"></a>Service Broker
L’application Portail d’entreprise Intune ou Microsoft Authenticator fournit le composant de service Broker. Le compte est créé dans AccountManager. Le type de compte est « com.microsoft.workaccount ». AccountManager autorise un seul compte d’authentification unique. Il crée un cookie d’authentification unique pour cet utilisateur après avoir terminé la demande d’appareil pour l’une des applications.

Pour en savoir plus sur la configuration à l’aide d’un service Broker, consultez [l’article wiki sur Broker](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker). 

### <a name="authority-url-and-ad-fs"></a>AD FS et URL de l’autorité
Les services de fédération Active Directory ne sont pas reconnus comme des services d’émission de jeton de sécurité (STS) de production ; vous devez donc activer la découverte de l’instance et transmettre la valeur false au constructeur AuthenticationContext.

L’URL de l’autorité a besoin d’une instance STS et d’un [nom de client](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Interrogation des éléments de cache
ADAL fournit un cache par défaut dans SharedPreferences avec quelques fonctions simples de requête de cache. Vous pouvez récupérer le cache actuel d’AuthenticationContext avec :

    ITokenCacheStore cache = mContext.getCache();

Vous pouvez également fournir votre implémentation de cache, si vous souhaitez le personnaliser.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Comportement d’invite
ADAL fournit une option permettant de spécifier le comportement d’invite. PromptBehavior.Auto affiche l’interface utilisateur si le jeton d’actualisation n’est pas valide et les informations d’identification utilisateur sont requises. PromptBehavior.Always ignore l’utilisation du cache et affiche toujours l’interface utilisateur.

### <a name="silent-token-request-from-cache-and-refresh"></a>Demande de jeton en mode silencieux à partir du cache et actualisation
Une demande de jeton en mode silencieux n’utilise pas l’affichage de l’interface utilisateur et ne nécessite pas d’activité. Elle renvoie un jeton à partir du cache s’il est disponible. Si le jeton est arrivé à expiration, cette méthode tente de l’actualiser. Si le jeton d’actualisation est arrivé à expiration ou a échoué, elle renvoie AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Vous pouvez également effectuer un appel de synchronisation à l’aide de cette méthode. Vous pouvez définir la valeur null pour un rappel ou utiliser acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostics
Voici les principales sources d’informations pour diagnostiquer les problèmes :

* Exceptions
* Journaux
* Suivis réseau

Notez que les ID de corrélation sont un élément central des diagnostics dans la bibliothèque. Vous pouvez définir vos ID de corrélation en fonction de chaque demande si vous souhaitez mettre en corrélation une demande de la bibliothèque d’authentification Azure AD (ADAL) avec d’autres opérations dans votre code. Si vous ne définissez aucun ID de corrélation, la bibliothèque ADAL génère un ID aléatoire. Tous les messages de journal et les appels réseau sont alors marqués avec l’ID de corrélation. L’ID généré automatiquement change à chaque demande.

#### <a name="errors--exceptions"></a>Erreurs et exceptions
Les exceptions sont le premier diagnostic. Nous essayons de fournir des messages d’erreur utiles. Si vous en trouvez un qui n’est pas utile, faites-le-nous savoir en signalant un problème. Incluez des informations sur l’appareil, notamment le modèle et le numéro du Kit de développement logiciel (SDK).

Pour plus d’informations sur les erreurs que votre application doit gérer, consultez [Meilleures pratiques de gestion des erreurs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling). 

#### <a name="logs"></a>Journaux
Vous pouvez configurer la bibliothèque pour générer des messages de journal que vous pouvez utiliser pour diagnostiquer les problèmes. Pour configurer la journalisation, effectuez l’appel suivant afin de configurer un rappel qui sera utilisé par ADAL pour transmettre chaque message de journal lorsqu’il sera généré.

Pour activer la journalisation, consultez [l’article wiki sur la journalisation](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging).

### <a name="session-cookies-in-webview"></a>Cookies de session dans WebView
Android WebView n’efface pas les cookies de session après la fermeture de l’application. Vous pouvez gérer cela à l’aide de l’exemple de code suivant :

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Pour plus d’informations sur les cookies, consultez les [informations concernant CookieSyncManager sur le site Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="ntlm-dialog-box"></a>Boîte de dialogue NTLM
ADAL version 1.1.0 prend en charge une boîte de dialogue NTLM qui est traitée via l’événement onReceivedHttpAuthRequest de WebViewClient. Vous pouvez personnaliser la disposition et les chaînes de cette boîte de dialogue.

### <a name="cross-app-sso"></a>Authentification unique entre applications
Découvrez [comment activer l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
