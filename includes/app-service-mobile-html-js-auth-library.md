---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177574"
---
### <a name="how-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Procédure : authentification auprès d’un fournisseur (flux serveur)
Pour que Mobile Apps gère le processus d’authentification dans votre application, vous devez inscrire votre application auprès de votre fournisseur d’identité. Ensuite, dans Azure App Service, vous devez configurer l’ID d’application et le secret fournis par votre fournisseur.
Pour plus d'informations, consultez le didacticiel [Ajout de l'authentification à votre application](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Une fois que vous avez inscrit votre fournisseur d'identité, appelez la méthode `.login()` avec le nom de votre fournisseur. Par exemple, pour vous connecter avec Facebook, utilisez le code suivant :

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Les valeurs valides pour le fournisseur sont « aad », « facebook », « google », « microsoftaccount » et « twitter ».

> [!NOTE]
> L’authentification Google ne fonctionne pour le moment pas via le flux serveur.  Pour s’authentifier auprès de Google, vous devez utiliser une [méthode gérée par le client](#client-auth).

Dans ce cas, Azure App Service gère le flux d’authentification OAuth 2.0.  Il affiche la page de connexion du fournisseur sélectionné et génère un jeton d’authentification App Service après avoir établi une connexion avec le fournisseur d’identité. La fonction de connexion, quand elle est utilisée, renvoie un objet JSON qui expose l’ID utilisateur et le jeton d’authentification App Service dans les champs userId et authenticationToken, respectivement. Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration.

### <a name="how-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Procédure : authentification auprès d’un fournisseur (flux client)

Votre application peut également contacter le fournisseur d’identité de manière indépendante, puis fournir le jeton renvoyé à App Service à des fins d’authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

#### <a name="social-authentication-basic-example"></a>Exemple de base de l’authentification sociale

Cet exemple utilise le SDK client Facebook pour l'authentification :

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Cet exemple part du principe que le jeton fourni par le Kit de développement logiciel (SDK) propre au fournisseur est stocké dans une variable token.

### <a name="how-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Procédure : obtention des informations sur l’utilisateur authentifié

Les informations d’authentification peuvent être récupérées du point de terminaison `/.auth/me` à l’aide d’un appel HTTP avec une bibliothèque AJAX.  Veillez à définir l’en-tête `X-ZUMO-AUTH` sur votre jeton d’authentification.  Le jeton d'authentification est stocké dans `client.currentUser.mobileServiceAuthenticationToken`.  Par exemple, pour utiliser l’API d’extraction :

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

L’extraction est disponible sous forme de [package npm](https://www.npmjs.com/package/whatwg-fetch) ou de téléchargement par navigateur à partir de [CDNJS](https://cdnjs.com/libraries/fetch). Vous pouvez également utiliser jQuery ou une autre API AJAX pour extraire les informations.  Les données sont reçues sous la forme d’un objet JSON.
