---
title: Recherche Web Bing dans une application web à page unique | Microsoft Docs
description: Montre comment utiliser l’API Recherche Web Bing dans une application web à page unique.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369989"
---
# <a name="tutorial-single-page-web-app"></a>Didacticiel : Application web à page unique

L’API Recherche Web Bing vous permet de parcourir le web et d’obtenir des résultats de différents types relatifs à une requête. Dans ce didacticiel, nous allons générer une application web à page unique qui utilise l’API Recherche Web Bing pour afficher les résultats de la recherche directement sur la page. L’application inclut des composants HTML, CSS et JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> Les en-têtes HTTP et JSON en bas de la page révèlent la réponse JSON et les informations de requête HTTP lorsque vous cliquez dessus. Ces détails sont utiles lorsque vous explorez le service.

L’application du didacticiel illustre les actions suivantes :

> [!div class="checklist"]
> * Effectuer un appel d’API Recherche Web Bing dans JavaScript
> * Transmettre des options de recherche à l’API Recherche Web Bing
> * Afficher des résultats de recherche (web, actualités, images et vidéos)
> * Parcourir les résultats de la recherche
> * Gérer l’ID de client Bing et la clé d’abonnement d’API
> * Gérer les erreurs qui peuvent se produire

La page du didacticiel est entièrement autonome ; elle n’utilise pas d’infrastructures, de feuilles de style ni même de fichiers image externes. Elle a uniquement recours à des fonctionnalités de langage JavaScript largement prises en charge et fonctionne avec les versions actuelles des principaux navigateurs web.

Dans ce didacticiel, nous abordons seulement certaines parties du code source. Le code source complet est disponible [sur une page distincte](tutorial-bing-web-search-single-page-app-source.md). Copiez et collez ce code dans un éditeur de texte, puis enregistrez-le en tant que `bing.html`.

## <a name="app-components"></a>Composants de l’application

Comme n’importe quelle application web à page unique, l’application du didacticiel comprend trois parties :

> [!div class="checklist"]
> * HTML : définit la structure et le contenu de la page
> * CSS : définit l’apparence de la page
> * JavaScript : définit le comportement de la page

Ce didacticiel n’aborde pas la plupart du code HTML ou CSS en détail, car il est simple.

Le code HTML contient le formulaire de recherche dans lequel l’utilisateur saisit une requête et choisit les options de recherche. Le formulaire est connecté à JavaScript, qui effectue la recherche selon la balise `onsubmit` de l’attribut `<form>` :

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
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

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

La balise `onsubmit` du code HTML `form` appelle la fonction `bingWebSearch` pour renvoyer les résultats de la recherche. `bingWebSearch` utilise `getSubscriptionKey` pour authentifier chaque requête. Comme indiqué dans la définition précédente, `getSubscriptionKey` demande une clé à l’utilisateur si la clé n’a été saisie. La clé est ensuite stockée en vue d’une utilisation continue par l’application.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Sélection des options de recherche

![[Formulaire Recherche Web Bing]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

Le formulaire HTML inclut des éléments avec les noms suivants :

| | |
|-|-|
| `where` | Menu déroulant pour sélectionner le marché (lieu et langue) utilisé pour la recherche. |
| `query` | Champ de texte dans lequel entrer les termes de recherche. |
| `what` | Cases à cocher pour la promotion de types particuliers de résultats. La promotion des images, par exemple, donne la priorité aux images. |
| `when` | Menu déroulant pour éventuellement limiter la recherche à un jour, une semaine ou un mois. |
| `safe` | Case à cocher indiquant s’il faut utiliser la fonctionnalité SafeSearch de Bing afin de filtrer les résultats pour « adultes ». |
| `count` | Champ masqué. Nombre de résultats de recherche à renvoyer pour chaque requête. À modifier pour afficher plus ou moins de résultats par page. |
| `offset` | Champ masqué. Décalage du premier résultat de recherche dans la requête ; utilisé pour la pagination. Réinitialisé sur `0` lors d’une nouvelle requête. |

> [!NOTE]
> L’API Recherche Web Bing offre encore plus de paramètres de requête. Nous n’en utilisons ici que quelques-uns.

La fonction JavaScript `bingSearchOptions()` convertit ces champs au format requis par l’API Recherche Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Par exemple, le paramètre `SafeSearch` dans un appel d’API réel peut être `strict`, `moderate` ou `off`, `moderate` étant la valeur par défaut. Notre formulaire, cependant, utilise une case à cocher qui a seulement deux états. Le code JavaScript convertit ce paramètre en la valeur `strict` ou `off` (`moderate` n’est pas utilisé).

Si au moins d’une des cases de **promotion** est cochée, nous ajoutons également un paramètre `answerCount` à la requête. `answerCount` est requis lorsque vous utilisez le paramètre `promote`. Nous l’avons simplement défini sur `9` (le nombre de types de résultats pris en charge par l’API Recherche Web Bing) pour obtenir un maximum de types de résultats.

> [!NOTE]
> La promotion d’un type de résultat ne *garantit* pas que les résultats de la recherche incluent ce type de résultat. Par contre, la promotion donne la priorité à ce type de résultats par rapport à son classement habituel. Pour limiter la recherche à des types particuliers de résultats, utilisez le paramètre de requête `responseFilter` ou appelez un point de terminaison plus spécifique, tels que Recherche d’images Bing ou Recherche d'actualités Bing.

Nous envoyons également les paramètres de requête `textDecoration` et `textFormat` afin que le terme de recherche apparaisse en gras dans les résultats. Ces valeurs sont codées en dur dans le script.

## <a name="performing-the-request"></a>Exécution de la requête

Selon la requête, la chaîne d’options et la clé d’API, la fonction `BingWebSearch` utilise un objet `XMLHttpRequest` pour effectuer la demande au point de terminaison Recherche Web Bing.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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

En cas de réussite de la requête HTTP, JavaScript appelle notre gestionnaire d’événements `load`, la fonction `handleBingResponse()`, pour gérer une requête HTTP GET réussie auprès de l’API. 

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> Une requête HTTP réussie ne signifie *pas* nécessairement que la recherche a réussi. Si une erreur se produit dans l’opération de recherche, l’API Recherche Web Bing renvoie un code d’état HTTP autre que 200 et inclut des informations sur l’erreur dans la réponse JSON. En outre, si la requête avait un débit limité, l’API renvoie une réponse vide.

Une grande partie du code dans les deux fonctions précédentes est dédiée à la gestion des erreurs. Des erreurs peuvent se produire aux étapes suivantes :

|Étape|Erreurs potentielles|Gérées par|
|-|-|-|
|Génération de l’objet de requête JavaScript|URL non valide|Bloc `try`/`catch`|
|Construction de la requête|Erreurs réseau, connexions abandonnées|Gestionnaires d’événements `error` et `abort`|
|Exécution de la recherche|Requête non valide, JSON non valide, limites de débit|Tests dans le gestionnaire d’événements `load`|

Les erreurs sont gérées en appelant `renderErrorMessage()` avec des détails connus de l’erreur. Si la réponse transmet tous les tests d’erreurs, nous appelons `renderSearchResults()` pour afficher les résultats de la recherche sur la page.

## <a name="displaying-search-results"></a>Gestion des résultats de la recherche

L’API Recherche Web Bing [exige d’afficher les résultats dans un ordre spécifié](useanddisplayrequirements.md). Étant donné que l’API peut renvoyer différents types de réponses, il n’est pas suffisant de procéder à une itération au sein de la collection `WebPages` de niveau supérieur dans la réponse JSON et d’afficher les résultats. (Si vous ne souhaitez obtenir qu’un seul type de résultats, utilisez le paramètre de requête `responseFilter` ou un autre point de terminaison Recherche Bing.)

Nous utilisons plutôt `rankingResponse` dans les résultats de la recherche pour classer l’affichage. Cet objet fait référence à des éléments des collections `WebPages`, `News`, `Images` et/ou `Videos`, ou d’autres collections de réponses de niveau supérieur dans la réponse JSON.

`rankingResponse` peut contenir jusqu'à trois collections des résultats de recherche, désignés par `pole`, `mainline` et `sidebar`. 

`pole`, le cas échéant, est le résultat de recherche le plus pertinent et doit être affiché en premier. `mainline` fait référence à l’ensemble des résultats de la recherche. Les résultats principaux doivent apparaître immédiatement après `pole` (ou en premier, si `pole` n’est pas présent). 

Enfin, `sidebar` fait référence aux résultats de recherche auxiliaires. Souvent, ces résultats sont associés à des recherches ou à des images. Si possible, ces résultats doivent apparaître dans une barre latérale. Si ce n’est pas pratique à cause des limites de l’écran (par exemple, sur un appareil mobile), ils doivent apparaître après le résultats `mainline`.

Chaque élément d’une collection `rankingResponse` fait référence à des éléments de résultats de recherche réels de deux manières différentes mais équivalentes.

| | |
|-|-|
|`id`|L’`id` ressemble à une URL, mais ne doit pas être utilisé pour les liens. Le type `id` d’un résultat de classement correspond à l’`id` d’un élément résultat de recherche dans une collection de réponses, *ou* une collection de réponses entière (telle que `Images`).
|`answerType`, `resultIndex`|`answerType` fait référence à la collection de réponses de niveau supérieur qui contient le résultat (par exemple, `WebPages`). `resultIndex` fait référence à l’index du résultat dans cette collection. Si `resultIndex` est omis, le résultat de classement fait référence à toute la collection.

> [!NOTE]
> Pour plus d’informations sur cette partie de la réponse de recherche, consultez la rubrique que le [classement des résultats](rank-results.md).

Vous pouvez utiliser la méthode la plus pratique dans votre application pour localiser l’élément de résultat de recherche référencé. Dans le code du didacticiel, nous utilisons `answerType` et `resultIndex` pour localiser chaque résultat de recherche.

Enfin, il est temps d’examiner notre fonction `renderSearchResults()`. Cette fonction effectue une itération sur les trois collections `rankingResponse` qui représentent les trois sections des résultats de recherche. Pour chaque section, nous appelons `renderResultsItems()` pour afficher les résultats correspondants.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` effectue à son tour une itération sur les éléments de chaque section `rankingResponse`, mappe chaque résultat de classement avec un résultat de recherche à l’aide des champs `answerType` et `resultIndex`, et appelle la fonction de rendu appropriée pour générer le code HTML du résultat. 

Si `resultIndex` n’est pas spécifié pour un élément de classement donné, `renderResultsItems()` effectue une itération sur tous les résultats de ce type et appelle la fonction de rendu pour chaque élément. 

Dans les deux cas, le code HTML obtenu est inséré dans l’élément `<div>` approprié de la page.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Rendu des éléments de résultat

Notre code JavaScript inclut un objet, `searchItemRenderers`, qui contient des fonctions de *renderer* générant du code HTML pour chaque type de résultat de recherche.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> L’application du didacticiel possède des renderers pour les pages web, les actualités, les images, les vidéos et les recherches connexes. Votre application a besoin de renderers pour les types de résultats que vous risquez de recevoir, tels que des calculs, des suggestions orthographiques, des entités, des fuseaux horaires et des définitions.

Certaines de nos fonctions de rendu acceptent uniquement le paramètre `item` : un objet JavaScript qui représente un résultat de recherche unique. D’autres acceptent des paramètres supplémentaires, qui peuvent être utilisés pour restituer les éléments différemment dans d’autres contextes. (Un renderer qui n’utilise pas ces informations n’a pas besoin d’accepter ces paramètres.)

Les arguments de contexte sont les suivants :

| | |
|-|-|
|`section`|Section de résultats (`pole`, `mainline` ou `sidebar`) dans lequel l’élément figure.
|`index`<br>`count`|Disponible lorsque l’élément `rankingResponse` spécifie que tous les résultats d’une collection donnée doivent être affichés ; `undefined` dans le cas contraire. Ces paramètres de réception l’index de l’élément dans sa collection et le nombre total d’éléments de la collection. Vous pouvez utiliser ces informations pour numéroter les résultats, afin de générer un code HTML différent pour le premier ou le dernier résultat, etc.|

Dans l’application du didacticiel,les deux renderers `images` et `relatedSearches` utilisent les arguments de contexte pour personnaliser le code HTML qu’ils génèrent. Examinons de plus près le renderer `images` :

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Notre fonction de renderer d’image :

> [!div class="checklist"]
> * calcule la taille des miniatures (largeur variable, mais hauteur fixe de 60 pixels) ;
> * insère le code HTML qui précède le résultat de l’image en fonction du contexte ;
> * génère la balise HTML `<a>` qui établit un lien vers la page contenant l’image ;
> * génère la balise HTML `<img>` pour afficher la miniature de l’image. 

Le renderer d’image utilise les variables `section` et `index` pour afficher les résultats différemment selon l’endroit où ils apparaissent. Un saut de ligne (balise `<br>`) est inséré entre les résultats d’images dans la barre latérale, afin de les présenter sous forme de colonne. Dans d’autres sections, le premier résultat d’image `(index === 0)` est précédé de la balise `<p>`. Sinon, les miniatures sont accolées les unes aux autres et renvoyées à la ligne si nécessaire dans la fenêtre du navigateur.

La taille des miniatures est utilisée à la fois dans la balise `<img>` et dans les champs `h` et `w` des URL de miniature. Le [service de miniature Bing](resize-and-crop-thumbnails.md) fournit alors une miniature d’exactement cette taille. Les attributs `title` et `alt` (description textuelle de l’image) sont construits à partir de nom de l’image et du nom d’hôte dans l’URL.

Les images s’affichent comme illustré ici dans les principaux résultats de recherche.

![[Résultats d’images Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>ID client persistant

Les réponses provenant de l’API Recherche Bing peuvent inclure un en-tête `X-MSEdge-ClientID`, qui doit être renvoyé à l’API avec les demandes suivantes. Si plusieurs API Recherche Bing sont utilisées, le même ID de client doit être employé avec toutes, si possible.

Le fait de fournir l’en-tête `X-MSEdge-ClientID` permet aux API Bing d’associer toutes les recherches d’un utilisateur, ce qui présente deux avantages essentiels.

Tout d’abord, cela permet au moteur de recherche Bing d’appliquer un contexte passé aux recherches pour trouver les résultats répondant le mieux à l’utilisateur. Si un utilisateur a précédemment recherché des termes liés à la navigation, par exemple, une recherche ultérieure sur « nœuds » peut éventuellement renvoyer des informations sur les nœuds de navigation.

Par ailleurs, Bing peut sélectionner au hasard des utilisateurs pour leur faire profiter des nouvelles fonctionnalités avant qu’elles ne deviennent disponibles au grand public. La fait de fournir le même ID de client avec chaque requête garantit que les utilisateurs choisis pour voir une fonctionnalité y aient toujours accès. Sans l’ID client, l’utilisateur peut voir une fonctionnalité apparaître et disparaître, de manière aléatoire, dans les résultats de recherche.

Les stratégies de sécurité de navigateur (CORS) peuvent rendre l’en-tête `X-MSEdge-ClientID` indisponible pour JavaScript. Cette limitation se produit lorsque la réponse de recherche a une origine différente de la page d’où provient la requête. Dans un environnement de production, vous devez gérer cette stratégie en hébergeant un script côté serveur qui effectue l’appel d’API sur le même domaine que la page web. Étant donné que le script a la même origine que la page web, l’en-tête `X-MSEdge-ClientID` est ensuite disponible pour JavaScript.

> [!NOTE]
> Dans une application eb de production, vous devez effectuer la requête côté serveur malgré tout. Dans le cas contraire, votre clé API Recherche Bing doit être incluse dans la page web, où elle est accessible à toute personne qui consulte la source. Vous êtes facturé pour toutes les utilisations associées à votre clé d’abonnement d’API, y compris les requêtes effectuées par des tiers non autorisés. Il est donc important de ne pas exposer votre clé.

À des fins de développement, vous pouvez effectuer la requête d’API Recherche Web Bing via un proxy CORS. La réponse émanant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui met les en-têtes de réponse sur liste verte et les rend disponibles pour JavaScript.

Il est facile d’installer un proxy CORS pour autoriser l’application du didacticiel à accéder à l’en-tête d’ID client. Tout d’abord, si ce n’est déjà fait, [installez Node.js](https://nodejs.org/en/download/). Exécutez alors la commande suivante dans une fenêtre de commande :

    npm install -g cors-proxy-server

Ensuite, remplacez le point de terminaison Recherche Web Bing dans le fichier HTML par :

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Enfin, démarrez le proxy CORS avec la commande suivante :

    cors-proxy-server

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du didacticiel ; la fermeture de la fenêtre entraîne l’arrêt du proxy. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel pour l’application mobile Recherche visuelle](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Référence pour l’API Recherche Web Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
