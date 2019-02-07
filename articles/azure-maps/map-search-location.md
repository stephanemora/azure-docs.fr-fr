---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Comment effectuer une demande de recherche avec Azure Maps, puis afficher les résultats sur une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c68b4bdffde5f987fe07d50d76fa83e7bdfa5235
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755036"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte.

Vous pouvez rechercher un lieu d’intérêt de deux façons. La première consiste à utiliser un module de service pour effectuer une demande de recherche. La seconde consiste à effectuer une demande de recherche en envoyant un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Les deux méthodes sont décrites ci-dessous.

## <a name="make-a-search-request-via-service-module"></a>Demande de recherche via un module de service

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code crée un objet carte et initialise le service client. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise la méthode [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) dans le [module de service](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). Elle vous permet d’effectuer une recherche de texte de forme libre via [l’API REST de recherche floue](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des points d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives. La réponse du service de recherche partielle est ensuite analysée au format GeoJSON à l’aide de la méthode [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest). 

Le troisième bloc de code crée un objet source de données à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) et y ajoute les résultats de la recherche. Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte.  La couche de symboles est ensuite créée, la source de données y est ajoutée et l’ensemble est ensuite ajouté à la carte.

Le dernier bloc de code ajuste les limites d’appareil photo pour la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

La requête de recherche, la source de données, la couche de symboles et les limites d’appareil photo sont créés et définis au sein du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que les résultats sont affichés après le chargement complet de la carte.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Demande de recherche via XMLHttpRequest

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map </a> (Afficher les résultats de recherche sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code envoie un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher le point d’intérêt. L’API de recherche floue peut gérer n’importe quelle combinaison d’entrées approximatives. 

Le troisième bloc de code analyse la réponse de la recherche et stocke les résultats dans un tableau pour calculer les limites. Il retourne ensuite les résultats de recherche.

Le quatrième bloc de code crée un objet source de données à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) et y ajoute les résultats de la recherche. Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. La couche de symboles est ensuite créée, la source de données y est ajoutée et l’ensemble est ensuite ajouté à la carte.

Le dernier bloc de code crée un objet [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) à l’aide du tableau de résultats, puis ajuste les limites de l’appareil photo pour la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte. Il affiche ensuite les épingles qui en résultent.

La requête de recherche, la source de données, la couche de symboles et les limites d’appareil photo sont définies au sein du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que les résultats sont affichés après le chargement complet de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur **Recherche approximative** :

> [!div class="nextstepaction"]
> [API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Obtenir des informations à partir de coordonnées](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)
