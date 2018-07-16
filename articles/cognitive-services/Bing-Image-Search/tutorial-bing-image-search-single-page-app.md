---
title: Recherche d’images Bing dans une application web à page unique | Microsoft Docs
description: Montre comment utiliser l’API Recherche d’images Bing dans une application web à page unique.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369988"
---
# <a name="tutorial-single-page-web-app"></a>Didacticiel : Application web à page unique

L’API Recherche d’images Bing vous permet de parcourir le web et d’obtenir des résultats d’image relatifs à la requête de recherche. Dans ce didacticiel, nous allons générer une application web à page unique qui utilise l’API Recherche d’images Bing pour afficher les résultats de la recherche directement sur la page. L’application inclut des composants HTML, CSS et JavaScript.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> Les en-têtes HTTP et JSON en bas de la page révèlent la réponse JSON et les informations de requête HTTP lorsque vous cliquez dessus. Ces détails sont utiles lorsque vous explorez le service.

L’application du didacticiel illustre les actions suivantes :

> [!div class="checklist"]
> * Effectuer un appel d’API Recherche d’images Bing dans JavaScript
> * Transmettre des options de recherche à l’API Recherche d’images Bing
> * Afficher les résultats de la recherche
> * Parcourir les résultats de la recherche
> * Gérer l’ID de client Bing et la clé d’abonnement d’API
> * Gérer les erreurs qui peuvent se produire

La page du didacticiel est entièrement autonome ; elle n’utilise pas d’infrastructures, de feuilles de style ni même de fichiers image externes. Elle a uniquement recours à des fonctionnalités de langage JavaScript largement prises en charge et fonctionne avec les versions actuelles des principaux navigateurs web.

Dans ce didacticiel, nous abordons seulement certaines parties du code source. Le code source complet est disponible [sur une page distincte](tutorial-bing-image-search-single-page-app-source.md). Copiez et collez ce code dans un éditeur de texte, puis enregistrez-le en tant que `bing.html`.

> [!NOTE]
> Ce didacticiel est très similaire au [didacticiel sur l’application de recherche Web Bing à page unique](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), mais traite uniquement des résultats de recherche d’images.

## <a name="app-components"></a>Composants de l’application

Comme n’importe quelle application web à page unique, l’application du didacticiel comprend trois parties :

> [!div class="checklist"]
> * HTML : définit la structure et le contenu de la page
> * CSS : définit l’apparence de la page
> * JavaScript : définit le comportement de la page

Ce didacticiel n’aborde pas la plupart du code HTML ou CSS en détail, car il est simple.

Le code HTML contient le formulaire de recherche dans lequel l’utilisateur saisit une requête et choisit les options de recherche. Le formulaire est connecté à JavaScript, qui effectue la recherche selon la balise `onsubmit` de l’attribut `<form>` :

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Le gestionnaire `onsubmit` renvoie `false`, ce qui empêche l’envoi du formulaire vers un serveur. Le code JavaScript effectue réellement le travail de collecte des informations nécessaires à partir du formulaire et exécute la recherche.

Le code HTML contient également les divisions (balises `<div>` HTML) où les résultats de recherche s’affichent.

## <a name="managing-subscription-key"></a>Gestion de la clé d’abonnement

Pour éviter d’avoir à inclure la clé d’abonnement de l’API Recherche Bing dans le code, nous utilisons le stockage persistant du navigateur pour stocker la clé. Si aucune clé n’est stockée, nous demandons la clé de l’utilisateur et la stockons pour une utilisation ultérieure. Si la clé est ensuite rejetée par l’API, nous invalidons la clé stockée et en demandons une autre à l’utilisateur.

Nous définissons les fonctions `storeValue` et `retrieveValue` qui utilisent l’objet `localStorage` (si le navigateur le prend en charge) ou un cookie. Notre fonction `getSubscriptionKey()` utilise ces fonctions pour stocker et récupérer la clé de l’utilisateur.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

La balise `onsubmit` du code HTML `<form>` appelle la fonction `bingWebSearch` pour renvoyer les résultats de la recherche. `bingWebSearch` utilise `getSubscriptionKey` pour authentifier chaque requête. Comme indiqué dans la définition précédente, `getSubscriptionKey` demande une clé à l’utilisateur si la clé n’a été saisie. La clé est ensuite stockée en vue d’une utilisation continue par l’application.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Sélection des options de recherche

![[Formulaire de Recherche d’images Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Le formulaire HTML comprend les contrôles suivants :

| | |
|-|-|
|`where`|Menu déroulant pour sélectionner le marché (lieu et langue) utilisé pour la recherche.|
|`query`|Champ de texte dans lequel saisir les termes de recherche.|
|`aspect`|Cases d’option pour choisir les proportions des images trouvées : carré approximatif, largeur ou hauteur.|
|`color`|Sélectionne couleur ou noir et blanc, ou une couleur prédominante.
|`when`|Menu déroulant pour éventuellement limiter la recherche à un jour, une semaine ou un mois.|
|`safe`|Case à cocher indiquant s’il faut utiliser la fonctionnalité Filtre adulte de Bing afin de filtrer les résultats pour « adultes ».|
|`count`|Champ masqué. Nombre de résultats de recherche à renvoyer pour chaque requête. À modifier pour afficher plus ou moins de résultats par page.|
|`offset`|Champ masqué. Décalage du premier résultat de recherche dans la requête ; utilisé pour la pagination. Réinitialisé sur `0` lors d’une nouvelle requête.|
|`nextoffset`|Champ masqué. Lorsqu’un résultat de la recherche est reçu, ce champ est défini sur la valeur de `nextOffset` dans la réponse. L’utilisation de ce champ permet d’éviter des résultats qui se chevauchent sur des pages successives.|
|`stack`|Champ masqué. Liste encodée JSON des décalages de pages précédentes de résultats de la recherche, pour revenir aux pages précédentes.|

> [!NOTE]
> L’API Recherche d’images Bing offre encore plus de paramètres de requête. Nous n’en utilisons ici que quelques-uns.

La fonction JavaScript `bingSearchOptions()` convertit ces champs en chaîne de requête partielle dans le format requis par l’API Recherche Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Par exemple, la fonctionnalité Filtre adulte peut être `strict`, `moderate` ou `off`. `moderate` est la valeur par défaut. Cependant, notre formulaire utilise une case à cocher qui a seulement deux états. Le code JavaScript convertit ce paramètre en la valeur `strict` ou `off` (nous n’utilisons pas `moderate`).

## <a name="performing-the-request"></a>Exécution de la requête

Selon la requête, la chaîne d’options et la clé d’API, la fonction `BingImageSearch` utilise un objet `XMLHttpRequest` pour effectuer la requête au point de terminaison Recherche d’images Bing.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

En cas de réussite de la requête HTTP, JavaScript appelle notre gestionnaire d’événements `load`, la fonction `handleBingResponse()`, pour gérer une requête HTTP GET réussie auprès de l’API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Une requête HTTP réussie ne signifie *pas* nécessairement que la recherche a réussi. Si une erreur se produit dans l’opération de recherche, l’API Recherche d’images Bing renvoie un code d’état HTTP autre que 200 et inclut des informations sur l’erreur dans la réponse JSON. En outre, si la requête avait un débit limité, l’API renvoie une réponse vide.

Une grande partie du code dans les deux fonctions précédentes est dédiée à la gestion des erreurs. Des erreurs peuvent se produire aux étapes suivantes :

|Étape|Erreurs potentielles|Gérées par|
|-|-|-|
|Génération de l’objet de requête JavaScript|URL non valide|Bloc `try`/`catch`|
|Construction de la requête|Erreurs réseau, connexions abandonnées|Gestionnaires d’événements `error` et `abort`|
|Exécution de la recherche|Requête non valide, JSON non valide, limites de débit|Tests dans le gestionnaire d’événements `load`|

Les erreurs sont gérées en appelant `renderErrorMessage()` avec des détails connus de l’erreur. Si la réponse transmet tous les tests d’erreurs, nous appelons `renderSearchResults()` pour afficher les résultats de la recherche sur la page.

## <a name="displaying-search-results"></a>Affichage des résultats de la recherche

La principale fonction d’affichage des résultats de la recherche est `renderSearchResults()`. Cette fonction utilise le JSON renvoyé par le service Recherche d’images Bing et restitue les résultats d’images et les recherches associées, le cas échéant.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Les principaux résultats de recherche d’images sont renvoyés en tant qu’objet `value` de niveau supérieur dans la réponse JSON. Nous les transmettons à notre fonction `renderImageResults()`, qui effectue une itération au sein de ces résultats et appelle une fonction distincte pour rendre chaque élément en HTML. Le code HTML résultant est renvoyé à `renderSearchResults()`, où il est inséré dans la division `results` de la page.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

L’API Recherche d’images Bing renvoie jusqu’à quatre types de résultats liés différents, chacun dans son propre objet de niveau supérieur. Il s'agit de :

|||
|-|-|
|`pivotSuggestions`|Requêtes qui remplacent un mot central de la recherche d’origine par un autre. Par exemple, si vous recherchez « fleurs rouges », « rouge » peut être un mot central et une suggestion peut être « fleurs jaunes ».|
|`queryExpansions`|Requêtes qui affinent votre recherche d’origine en ajoutant d’autres termes. Par exemple, si vous recherchez « Microsoft Surface », une expansion de la requête peut être « Microsoft Surface Pro ».|
|`relatedSearches`|Requêtes qui ont également été entrées par d’autres utilisateurs qui ont entré la recherche d’origine. Par exemple, si vous recherchez « Mont Rainier », une recherche associée peut être « Mt. Sainte-Hélène ».|
|`similarTerms`|Requêtes dont le sens est similaire à celui de la recherche d’origine. Par exemple, si vous recherchez « chatons », un terme similaire peut être « mignon ».|

Comme nous l’avons vu précédemment dans `renderSearchResults()`, nous rendons uniquement les suggestions `relatedItems` et plaçons les liens qui en résultent dans la barre latérale de la page.

## <a name="rendering-result-items"></a>Rendu des éléments de résultat

Notre code JavaScript inclut un objet, `searchItemRenderers`, qui contient des fonctions de *renderer* générant du code HTML pour chaque type de résultat de recherche.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Une fonction de renderer peut accepter les paramètres suivants :

| | |
|-|-|
|`item`|L’objet JavaScript contenant les propriétés de l’élément, telles que son URL et sa description.|
|`index`|L’index de l’élément de résultat dans sa collection.|
|`count`|Le nombre d’éléments dans la collection de l’élément du résultat de recherche.|

Les paramètres `index` et `count` peuvent être utilisés pour compter les résultats, pour générer un code HTML spécial pour le début ou la fin d’une collection, pour insérer des sauts de ligne après un certain nombre d’éléments et ainsi de suite. Si un renderer n’a pas besoin de cette fonctionnalité, il est inutile d’accepter ces deux paramètres.

Examinons de plus près le renderer `images` :

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Notre fonction de renderer d’image :

> [!div class="checklist"]
> * Calcule la taille des miniatures (largeur variable, avec un minimum de 120 pixels, mais hauteur fixe de 90 pixels).
> * Génère la balise HTML `<img>` pour afficher l’image miniature. 
> * Génère les balises HTML `<a>` qui établissent un lien vers l’image et la page la contenant.
> * Génère la description qui affiche des informations sur l’image et le site sur lequel elle se trouve.

Nous testons la variable `index` afin d’insérer une balise `<p>` avant le premier résultat d’image. Sinon, les miniatures sont accolées les unes aux autres et renvoyées à la ligne si nécessaire dans la fenêtre du navigateur.

La taille des miniatures est utilisée à la fois dans la balise `<img>` et dans les champs `h` et `w` des URL de miniature. Le [service de miniature Bing](resize-and-crop-thumbnails.md) fournit alors une miniature d’exactement cette taille.

## <a name="persisting-client-id"></a>ID client persistant

Les réponses provenant des API Recherche Bing peuvent inclure un en-tête `X-MSEdge-ClientID`, qui doit être renvoyé à l’API avec les requêtes suivantes. Si plusieurs API Recherche Bing sont utilisées, le même ID de client doit être employé avec toutes, si possible.

Le fait de fournir l’en-tête `X-MSEdge-ClientID` permet aux API Bing d’associer toutes les recherches d’un utilisateur, ce qui présente deux avantages essentiels.

Tout d’abord, cela permet au moteur de recherche Bing d’appliquer un contexte passé aux recherches pour trouver les résultats répondant le mieux à l’utilisateur. Si un utilisateur a précédemment recherché des termes liés à la navigation, par exemple, une recherche ultérieure sur « nœuds » peut éventuellement renvoyer des informations sur les nœuds de navigation.

Par ailleurs, Bing peut sélectionner au hasard des utilisateurs pour leur faire profiter des nouvelles fonctionnalités avant qu’elles ne deviennent disponibles au grand public. Le fait de fournir le même ID de client avec chaque requête garantit que les utilisateurs choisis pour voir une fonctionnalité y aient toujours accès. Sans l’ID client, l’utilisateur peut voir une fonctionnalité apparaître et disparaître, de manière aléatoire, dans les résultats de recherche.

Les stratégies de sécurité de navigateur (CORS) peuvent rendre l’en-tête `X-MSEdge-ClientID` indisponible pour JavaScript. Cette limitation se produit lorsque la réponse de recherche a une origine différente de la page d’où provient la requête. Dans un environnement de production, vous devez gérer cette stratégie en hébergeant un script côté serveur qui effectue l’appel d’API sur le même domaine que la page web. Étant donné que le script a la même origine que la page web, l’en-tête `X-MSEdge-ClientID` est ensuite disponible pour JavaScript.

> [!NOTE]
> Dans une application web de production, vous devez effectuer la requête côté serveur malgré tout. Dans le cas contraire, votre clé API Recherche Bing doit être incluse dans la page web, où elle est accessible à toute personne qui consulte la source. Vous êtes facturé pour toutes les utilisations associées à votre clé d’abonnement d’API, y compris les requêtes effectuées par des tiers non autorisés. Il est donc important de ne pas exposer votre clé.

À des fins de développement, vous pouvez effectuer la requête d’API Recherche Web Bing via un proxy CORS. La réponse émanant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui met les en-têtes de réponse sur liste verte et les rend disponibles pour JavaScript.

Il est facile d’installer un proxy CORS pour autoriser l’application du didacticiel à accéder à l’en-tête d’ID client. Tout d’abord, [installez Node.js](https://nodejs.org/en/download/) si ce n’est pas déjà fait. Exécutez alors la commande suivante dans une fenêtre de commande :

    npm install -g cors-proxy-server

Ensuite, remplacez le point de terminaison Recherche Web Bing dans le fichier HTML par :

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Enfin, lancez le proxy CORS avec la commande suivante :

    cors-proxy-server

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du tutoriel ; si vous fermez la fenêtre, le proxy s’arrête. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de la recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Recherche d’images Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

