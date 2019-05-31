---
title: Utiliser le module de services - Azure Maps | Microsoft Docs
description: Découvrez comment utiliser le module de services Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: b56191bc93a91f944bb313b4ab9ad602da17dcf0
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357650"
---
# <a name="use-the-azure-maps-services-module"></a>Utiliser le module de services Azure Maps

Le Kit de développement logiciel Azure Maps Web fournit un *module services*. Ce module est une bibliothèque d’assistance qui le rend facile à utiliser les services Azure Maps REST dans les applications web ou Node.js en utilisant JavaScript ou TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Utiliser le module de services dans une page Web

1. Créez un fichier HTML.
1. Charger le module de services Azure Maps. Vous pouvez y charger de deux manières :
    - Utilisez la version d’Azure Content Delivery Network hébergée dans le monde entier, du module de services Azure Maps. Ajouter une référence de script à la `<head>` élément du fichier :

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - Vous pouvez également charger le code source du SDK Web de Azure mappe localement à l’aide la [rest d’azure maps](https://www.npmjs.com/package/azure-maps-rest) npm du package, puis l’hôte avec votre application. Ce package inclut aussi des définitions de TypeScript. Utilisez cette commande :
    
        > **npm install azure-maps-rest**
    
        Ensuite, ajoutez une référence de script à la `<head>` élément du fichier :

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Créer un pipeline d’authentification. Vous devez créer le pipeline avant que vous pouvez initialiser un point de terminaison de service URL client. Utilisez votre propre clé de compte Azure Maps ou les informations d’identification Azure Active Directory (Azure AD) pour authentifier un client de service de recherche de tables Azure. Dans cet exemple, le client de URL de service de recherche est créé. 

    Si vous utilisez une clé d’abonnement pour l’authentification :

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Si vous utilisez Azure AD pour l’authentification :

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
    try {
      console.log("Renewing token");
      var token = await getAadToken();
      tokenCredential.token = token;
      tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
    } catch (error) {
      console.log("Caught error when renewing token");
      clearTimeout(tokenRenewalTimer);
      throw error;
    }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
    retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        // Use the signed-in auth context to get a token.
        return new Promise((resolve, reject) => {
            // The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        // Decode the JSON Web Token (JWT) to get the expiration time stamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        // Return the milliseconds remaining until the token must be renewed.
        // Reduce the time until renewal by 5 minutes to avoid using an expired token.
        // The exp property is the time stamp of the expiration, in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Pour plus d’informations, consultez [l’authentification avec Azure Maps](azure-maps-authentication.md).

1. Le code suivant utilise le client URL du service recherche Azure nouvellement créé à Géocode une adresse : "1 Microsoft Way, Redmond, WA". Le code utilise le `searchAddress` de fonction et affiche les résultats sous la forme d’une table dans le corps de la page.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];

      // Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');

      // Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
          response.results[i].address.freeformAddress, 
          '</td><td>', 
          response.results[i].position.lat,
          '</td><td>', 
          response.results[i].position.lon,
          '</td></tr>');
      }

      html.push('</table>');

      // Add the resulting HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Voici la complète en cours d’exécution des exemples de code :

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="L’utilisation du Module de Services" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>à l’aide du Module de Services</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Pour d’autres exemples de code qui utilisent le module de services, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Afficher les résultats de la recherche sur la carte](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)