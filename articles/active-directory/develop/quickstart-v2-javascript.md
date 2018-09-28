---
title: Démarrage rapide Azure AD v2 JavaScript | Microsoft Docs
description: Découvrez comment les applications JavaScript peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4c64552ab23331755bf1d292bede61e78b339df0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987422"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Démarrage rapide : Connecter des utilisateurs et acquérir un jeton d’accès à partir d’une application JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Dans ce démarrage rapide, vous allez apprendre à utiliser un exemple de code qui montre comment une application à page unique (SPA) JavaScript peut connecter des comptes personnels, professionnels et scolaires, obtenir un jeton d’accès et appeler l’API Graph Microsoft.

![Fonctionnement de l’exemple d’application généré par ce démarrage rapide](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Inscrivez votre application et téléchargez votre application de démarrage rapide
>
> ### <a name="step-1-register-your-application"></a>Étape 1 : inscrivez votre application
>
> 1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
> 1. Dans la zone **Nom de l’application**, attribuez un nom à votre application.
> 1. Vérifiez que la case **Guided Setup** (Installation guidée) est décochée et sélectionnez **Créer**.
> 1. Cliquez sur **Ajouter une plateforme**, puis sélectionnez **Web**.
> 1. Vérifiez que la case **Autoriser un flux implicite** est *cochée*.
> 1. Sous **URL de redirection**, ajoutez `http://localhost:30662/`.
> 1. Cliquez sur **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : configurez votre application dans le portail Azure
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter un URI de redirection tel que `http://localhost:30662/` et activer **Octroi implicite**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs

#### <a name="step-2-download-the-project"></a>Étape 2 : téléchargez le projet

Vous pouvez choisir l’une de ces options en fonction de leur adéquation avec votre environnement de développement.
* [Télécharger les principaux fichiers de projet pour un serveur web, tel que Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)
* [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)

Extrayez le fichier zip dans un dossier local (par exemple, **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Étape 3 : configurez une application JavaScript

> [!div renderon="docs"]
> Modifiez `msalconfig.js` et remplacez `Enter_the_Application_Id_here` par l’ID d’application que vous venez d’enregistrer. Vous pouvez trouver *l’ID d’application* sur la page *Vue d’ensemble*.

> [!div class="sxs-lookup" renderon="portal"]
> Modifiez `msalconfig.js` et remplacez msalconfig par :

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
> [!NOTE]
> Si vous utilisez [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), l’URI de redirection est définie sur `http://localhost:30662/`, tel que configuré dans le projet de l’exemple de code. Si vous utilisez [Node.js](https://nodejs.org/en/download/) ou tout autre serveur web, définissez l’URI de redirection sur `http://localhost:30662/`, puis configurez le serveur pour démarrer l’écoute sur ce port.
>

#### <a name="step-4-run-the-project"></a>Étape 4 : exécutez le projet

Si vous utilisez Visual Studio, appuyez sur **F5** pour exécuter votre projet.

Si vous utilisez Node.js, vous pouvez exécuter la commande suivante sur une ligne de commande à partir du répertoire du projet pour démarrer le serveur :
 ```batch
 npm install
 node server.js
 ```
Ouvrez un navigateur web et accédez à `http://localhost:30662/`. Cliquez sur le bouton **Call Microsoft Graph API** (Appeler l’API Graph Microsoft) pour démarrer la connexion.


## <a name="more-information"></a>Informations complémentaires

### <a name="msaljs"></a>*msal.js*

MSAL est la bibliothèque utilisée pour connecter des utilisateurs et demander des jetons permettant d’accéder à une API protégée par Microsoft Azure Active Directory (Azure AD). Le fichier *index.html* du démarrage rapide contient une référence à la bibliothèque :

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
````

Sinon, si Node est installé, vous pouvez le télécharger via npm :

```batch
npm install msal
```

### <a name="msal-initialization"></a>Initialisation MSAL

Le code de démarrage rapide montre également comment initialiser la bibliothèque :

```javascript
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |ID d’application de l’application inscrite dans le portail Azure|
> |`authority`    |Il s’agit de l’URL de l’autorité. La transmission *null* définit l’autorité par défaut sur `https://login.microsoftonline.com/common`. Si votre application est à locataire unique (c’est-à-dire qu’elle cible des comptes dans un répertoire seulement), définissez cette valeur sur `https://login.microsoftonline.com/<tenant name or ID>`|
> |`loginCallBack`| La méthode de rappel appelée après l’authentification redirige vers l’application|
> |`redirectUri`  |URL vers laquelle les utilisateurs sont redirigés après l’authentification avec Azure AD|

### <a name="sign-in-users"></a>Connexion des utilisateurs

L’extrait de code qui suit montre comment connecter des utilisateurs :

```javascript
userAgentApplication.loginRedirect(graphAPIScopes);
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | (Facultatif) Contient les étendues demandées pour le consentement de l’utilisateur au moment de l’ouverture de session (ex : `[ "user.read" ]` pour Microsoft Graph ou `[ "api://<Application ID>/access_as_user" ]` pour les API web personnalisées). |

> [!TIP]
> Vous pouvez également utiliser la méthode `loginPopup` pour afficher une fenêtre contextuelle afin de connecter l’utilisateur.

### <a name="request-tokens"></a>Requête de jetons

MSAL utilise trois méthodes pour acquérir des jetons : `acquireTokenRedirect`, `acquireTokenPopup` et `acquireTokenSilent` :

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

La méthode `acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand la méthode `loginRedirect` ou `loginPopup` est exécutée pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants. Les appels pour les demandes ou les renouvellements de jetons sont effectués en mode silencieux.

```javascript
// Try to acquire the token used to query Graph API silently first:
userAgentApplication.acquireTokenSilent(graphAPIScopes)
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | Contient les étendues que vous avez demandé à retourner dans le jeton d’accès pour l’API (ex : `[ "user.read" ]` pour Microsoft Graph ou `[ "api://<Application ID>/access_as_user" ]` pour les API web personnalisées). |

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

 Dans certaines situations, vous devez forcer les utilisateurs à interagir avec le point de terminaison Azure AD v2.0. Par exemple : 
* Les utilisateurs doivent réentrer leurs informations d’identification, car leur mot de passe a expiré
* Votre application demande l’accès à des étendues de ressource supplémentaires pour laquelle l’utilisateur doit donner son consentement
* Une authentification à 2 facteurs est demandée.

Le modèle habituellement recommandé pour la plupart des applications est le suivant : `acquireTokenSilent` d’abord, puis intercepter l’exception et appeler `acquireTokenRedirect` (ou `acquireTokenPopup`) pour démarrer une requête interactive.

Le fait d’appeler `acquireTokenRedirect(scope)` entraîne la redirection des utilisateurs vers le point de terminaison Azure AD v2.0 (ou l’appel de `acquireTokenPopup(scope)` entraîne l’affichage d’une fenêtre contextuelle) avec lequel ils doivent interagir en confirmant leurs informations d’identification, en consentant à ce que l’application accède à la ressource ou en effectuant l’authentification à 2 facteurs.

```javascript
userAgentApplication.acquireTokenRedirect(graphAPIScopes);
```

## <a name="next-steps"></a>Étapes suivantes

Pour un guide pas à pas plus détaillé sur la création de l’application pour ce démarrage rapide, consultez le didacticiel de JavaScript ci-dessous.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Découvrez les étapes permettant de créer l’application pour ce démarrage rapide

> [!div class="nextstepaction"]
> [Didacticiel pour appeler l’API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Parcourir le référentiel MSAL pour la documentation, les FAQ, les problèmes et ainsi de suite

> [!div class="nextstepaction"]
> [Référentiel GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
