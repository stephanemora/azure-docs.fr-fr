---
title: Configuration guidée d’application monopage (SPA) JavaScript Azure AD v2.0 | Microsoft Docs
description: Comment les applications JavaScript SPA peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: cee0884ef20ef9cfd63d81d6f223705d34c65ccb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511779"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Connecter les utilisateurs et appeler l’API Microsoft Graph à partir d’une application monopage (SPA) JavaScript

Ce guide explique comment une application monopage JavaScript peut se connecter à des comptes personnels, scolaires et professionnels, obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant du point de terminaison de la plateforme d’identités Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

L’exemple d’application créé dans ce guide permet à une application monopage JavaScript d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons provenant du point de terminaison de la plateforme d’identités Microsoft. Dans ce scénario, une fois l’utilisateur authentifié, un jeton d’accès est demandé et ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Bibliothèque d’authentification Microsoft pour JavaScript Preview|

> [!NOTE]
> *msal.js* cible le *point de terminaison de la plateforme d’identités Microsoft* qui permet aux comptes personnels, scolaires et professionnels de se connecter et d’obtenir les jetons nécessaires. Le *point de terminaison de la plateforme d’identités Microsoft* a [plusieurs limitations](azure-ad-endpoint-comparison.md#limitations).
> Pour comprendre les différences entre les points de terminaison v1.0 et v2.0, consultez [Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Configurer le serveur web ou projet

> Vous préférez télécharger le projet de cet exemple ? Effectuez l’une des actions suivantes :
> 
> - [Téléchargez les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour exécuter le projet avec un serveur web local, tel que Node.
>
> - (Facultatif) [Téléchargez le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip) pour exécuter le projet avec le serveur IIS.
>
> Passez ensuite à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Prérequis

* L’exécution de ce didacticiel requiert un serveur web local tel que [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Si vous utilisez Node.js pour exécuter le projet, installez un environnement de développement intégré (IDE), tel que [Visual Studio Code](https://code.visualstudio.com/download), pour modifier les fichiers de projet.

* Les instructions de ce guide reposent sur Node.js et Visual Studio 2017. Toutefois, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Option 1 : Node.js ou autres serveurs web
> Assurez-vous que vous avez installé [Node.js](https://nodejs.org/en/download/), puis procédez comme suit :
> - Créez un dossier pour héberger votre application.
>
> ### <a name="option-2-visual-studio"></a>Option 2 : Visual Studio
> Si vous utilisez Visual Studio et créez un nouveau projet, procédez comme suit :
> 1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.
> 1. Sous **Visual C#\Web**, sélectionnez **Application web ASP.NET (.NET Framework)** .
> 1. Entrez un nom pour votre application, puis sélectionnez **OK**.
> 1. Sous **Nouvelle application web ASP.NET**, sélectionnez **Vide**.

## <a name="create-the-spa-ui"></a>Créer l’interface utilisateur de SPA
1. Créez un fichier *index.html* pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez **Ajouter** > **Nouvel élément** > **Page HTML**, puis nommez le fichier *index.html*.

1. Dans le fichier *index.html*, ajoutez le code suivant :

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Vous pouvez remplacer la version de MSAL.js dans le script précédent par la dernière version publiée sous [Publications MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utiliser Microsoft Authentication Library (MSAL) pour connecter l’utilisateur

1. Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

Quand un utilisateur clique sur le bouton **Se connecter** pour la première fois, la méthode `signIn` appelle `loginPopup` pour le connecter. Cette méthode entraîne l’ouverture d’une fenêtre contextuelle avec le *point de terminaison de la plateforme d’identités Microsoft* afin de demander et valider les informations d’identification de l’utilisateur. Une fois connecté, l’utilisateur est redirigé vers la page *index.html* d’origine. Ensuite, un jeton est reçu, puis traité par `msal.js`, et les informations contenues dans le jeton sont mises en cache. Ce jeton est le *jeton d’ID* et contient des informations de base sur l’utilisateur, telles que son nom d’affichage. Si vous envisagez d’utiliser les données fournies par ce jeton à toutes fins que ce soit, vous devez vous assurer que ce jeton est validé par le serveur principal afin de garantir qu’il a été émis pour un utilisateur valide de votre application.

L’application SPA créée dans ce guide appelle `acquireTokenSilent` et/ou `acquireTokenPopup` pour acquérir un *jeton d’accès* utilisé pour demander les informations de profil utilisateur à l’API Microsoft Graph. Si vous avez besoin d’un exemple qui valide le jeton d’ID, regardez [cet exemple d’application](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "exemple GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2") dans GitHub. Cet exemple utilise une API web ASP.NET pour la validation du jeton.

#### <a name="getting-a-user-token-interactively"></a>Obtention d’un jeton d’utilisateur de manière interactive

Après la première connexion, il n’est pas souhaitable de demander aux utilisateurs de se réauthentifier chaque fois qu’ils ont besoin d’un jeton pour accéder à une ressource. Par conséquent, *acquireTokenSilent* doit être utilisé pour acquérir des jetons dans la plupart des situations. Il existe cependant des situations dans lesquelles vous devez forcer les utilisateurs à interagir avec le point de terminaison de la plateforme d’identités Microsoft. Voici quelques exemples de telles situations :

- Les utilisateurs doivent réentrer leurs informations d’identification, car le mot de passe a expiré.
- Votre application demande l’accès à une ressource pour laquelle l’utilisateur doit donner son consentement.
- Une authentification à 2 facteurs est demandée.

Le fait d’appeler *acquireTokenRedirect* entraîne l’affichage d’une fenêtre contextuelle (ou l’appel de *acquireTokenRedirect* entraîne la redirection des utilisateurs vers le point de terminaison de la plateforme d’identités Microsoft), où les utilisateurs doivent interagir en confirmant leurs informations d’identification, en consentant à ce que l’application accède à la ressource requise ou en effectuant l’authentification à 2 facteurs.

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux

La méthode `acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `loginPopup` (ou `loginRedirect`) est exécuté pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, car les appels de demande ou de renouvellement des jetons sont émis de manière silencieuse.
`acquireTokenSilent` risque d’échouer dans certains cas (par exemple, si le mot de passe a expiré). Votre application peut gérer cette exception de deux manières :

1. En appelant immédiatement `acquireTokenPopup` pour que l’utilisateur soit invité à se connecter. Cette méthode est couramment employée avec les applications en ligne dans lesquelles aucun contenu non authentifié n’est disponible pour l’utilisateur. L’exemple créé avec cette installation guidée utilise ce modèle.

2. Les applications peuvent également signaler à l’utilisateur qu’une connexion interactive est requise afin qu’il puisse choisir le bon moment pour se connecter ou que l’application puisse réessayer d’exécuter `acquireTokenSilent` ultérieurement. Cette solution est couramment retenue lorsque l’utilisateur peut utiliser d’autres fonctionnalités de l’application sans être perturbé, notamment lorsque du contenu non authentifié est disponible dans l’application. Dans ce cas, l’utilisateur peut décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes.

> [!NOTE]
> En raison d’un [problème connu](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) lié à la gestion des fenêtres contextuelles par le navigateur Internet Explorer, ce guide de démarrage rapide utilise les méthodes `loginRedirect` et `acquireTokenRedirect`.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans l’exemple d’application créé par ce guide, la méthode `callMSGraph()` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui nécessite un jeton, puis pour retourner le contenu à l’appelant. Cette méthode ajoute le jeton acquis à l’*en-tête d’autorisation HTTP*. Dans l’exemple d’application créé par ce guide, la ressource est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Inscrivez votre application

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez le compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
1. Sous la section **URI de redirection**, dans la liste déroulante, sélectionnez la plateforme **Web**, puis définissez la valeur sur l’URL de l’application basée sur votre serveur web. 

   Pour plus d’informations sur la définition et l’obtention de l’URL de redirection dans Visual Studio et Node.js, consultez les deux sections suivantes.

1. Sélectionnez **Inscription**.
1. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
1. Ce démarrage rapide requiert l’activation du [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md). Dans le volet gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et les jetons d’accès sont nécessaires, car cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Définir d’une URL de redirection pour Node.js
> Pour Node.js, vous pouvez définir le port du serveur web dans le fichier *server.js*. Ce didacticiel utilise le port 30662 pour référence, mais vous pouvez utiliser tout autre port disponible. 
>
> Pour configurer une URL de redirection dans les informations d’inscription de l’application, retournez dans le volet **Inscription d’application** et effectuez l’une des opérations suivantes :
>
> - Définissez *`http://localhost:30662/`* en tant qu’**URL de redirection**.
> - Si vous utilisez un port TCP personnalisé, utilisez *`http://localhost:<port>/`* (où *\<port>* correspond au numéro du port TCP personnalisé).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Définir une URL de redirection pour Visual Studio
> Pour obtenir l’URL de redirection pour Visual Studio, procédez comme suit :
> 1. Dans l’**Explorateur de solutions**, sélectionnez le projet.
>
>    La fenêtre **Propriétés** apparaît. Si ce n’est pas le cas, appuyez sur **F4**.
>
>    ![Fenêtre Propriétés du projet JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copiez la valeur d’**URL**.
 
> 1. Basculez vers le volet **Inscriptions des applications**, collez la valeur en tant **qu’URL de redirection**.

#### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1. Dans le fichier *index.html* que vous avez créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Dans le haut du fichier, entre les balises `<script></script>`, ajoutez le code suivant :

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Où :
    - *\<Enter_the_Application_Id_here>* est l’**ID d’application (client)** de l’application que vous avez inscrite.
    - *\<Enter_the_Tenant_info_here >* est défini sur une des options suivantes :
       - Si votre application prend en charge *Comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **nom du locataire** (par exemple, *contoso.microsoft.com*).
       - Si votre application prend en charge *Comptes dans un annuaire organisationnel*, remplacez cette valeur par **organizations**.
       - Si votre application prend en charge *Comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par **common**. Pour limiter la prise en charge aux *Comptes Microsoft personnels uniquement*, remplacez cette valeur par **consumers**.

## <a name="test-your-code"></a>Test de votre code

Testez votre code à l’aide des environnements suivants.

### <a name="test-with-nodejs"></a>Tester avec Node.js

Si vous n’utilisez pas Visual Studio, vérifiez que votre serveur web est démarré.

1. Configurez le serveur pour écouter un port TCP basé sur l’emplacement de votre fichier *index.html*. Pour Node.js, démarrez le serveur web afin d’écouter le port en exécutant les commandes ci-après dans une invite de ligne de commande à partir du dossier d’application :

    ```bash
    npm install
    node server.js
    ```
1. Dans votre navigateur, entrez **http://\<span >\</span > localhost:30662** ou **http://\<span >\</span > localhost : {port}** , où *port* est le port que votre serveur web écoute. Vous devez voir apparaître le contenu de votre fichier *index.html*, ainsi que le bouton **Se connecter**.

### <a name="test-with-visual-studio"></a>Effectuer des tests avec Visual Studio

Si vous utilisez Visual Studio, sélectionnez la solution de projet, puis appuyez sur F5 pour exécuter votre projet. Le navigateur accède à l’emplacement http://<span></span>localhost:{port}, et vous devriez voir le bouton **Se connecter**.

## <a name="test-your-application"></a>Tester votre application

Une fois que le navigateur a chargé votre fichier *index.html*, sélectionnez **Se connecter**. Vous êtes invité à vous connecter avec le point de terminaison de la plateforme d’identités Microsoft :

![La fenêtre de connexion au compte JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application

La première fois que vous vous connectez à votre application, vous êtes invité à lui donner accès à votre profil et à vous connecter :

![La fenêtre « Autorisations nécessaires »](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Afficher les résultats de l’application

Une fois connecté, vos informations de profil utilisateur renvoyées dans la réponse de l’API Microsoft Graph apparaissent sur la page.

![Résultats de l’appel à l’API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph requiert l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`.

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

Si une API principale ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser *clientId* en tant qu’étendue dans les appels pour acquérir des jetons.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)