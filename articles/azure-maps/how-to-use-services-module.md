---
title: L’utilisation du module de Services - Azure Maps | Microsoft Docs
description: Découvrez comment utiliser le module de services Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 335e114fc6a4afa83a6b82509148cf258a9e8347
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501150"
---
# <a name="using-the-azure-maps-services-module"></a>L’utilisation du module de Services de Azure Maps

Le Kit de développement logiciel Azure Maps Web fournit un module de services qui est une bibliothèque d’assistance qui permet de facilement utiliser les services Azure Maps REST dans les applications web ou Node.js à l’aide de JavaScript ou TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>À l’aide du module de services dans une page web

1. Créez un fichier HTML.
2. Charger dans le module de Services de Azure Maps. Cela est possible à l’aide de l’une des deux options suivantes.

    a. Utilisez la version CDN hébergée dans le monde entier du module de services Azure Maps en ajoutant une référence de script à la <head> élément du fichier :
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. Vous pouvez également charger le code source de kit de développement logiciel de Azure mappe Web localement à l’aide la [rest d’azure maps](https://www.npmjs.com/package/azure-maps-rest) NPM empaqueter et hébergez-le avec votre application. Ce package inclut aussi des définitions de TypeScript.
    
    > npm install azure-maps-rest
    
    Puis ajoutez un script fait référence à la `<head>` élément du fichier :
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Pour initialiser un point de terminaison de service URL client, vous devez d’abord créer un pipeline d’authentification. Utilisez votre propre clé de compte Azure Maps ou les informations d’identification Azure Active Directory (AAD) pour authentifier le client de service de recherche. Dans cet exemple, le client de URL de service de recherche est créé. Si vous utilisez une clé d’abonnement pour l’authentification :

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    En cas d’utilisation d’Azure Active Directory (AAD) pour l’authentification :

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
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
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
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
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Pour plus d’informations, consultez [l’authentification avec Azure Maps](azure-maps-authentication.md).

4. Le code suivant utilise le client de URL de service de recherche nouvellement créé pour Géocode une adresse, à l’aide de « 1 Microsoft Way, Redmond, WA » le `searchAddress` de fonction et afficher les résultats sous forme de table dans le corps de la page. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
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
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Voici l’exemple de code complet en cours d’exécution :

<iframe height="500" style="width: 100%;" scrolling="no" title="L’utilisation du Module de Services" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>à l’aide du Module de Services</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Consultez les articles suivants pour d’autres exemples de code qui utilisent le module de services :

> [!div class="nextstepaction"]
> [Afficher les résultats de la recherche sur la carte](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)