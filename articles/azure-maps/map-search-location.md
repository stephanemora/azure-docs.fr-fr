---
title: Afficher les résultats de la recherche sur une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à effectuer une demande de recherche à l’aide du SDK web Microsoft Azure Maps et à afficher les résultats sur la carte.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7123bfda0baa3054dbed336dc4bed8958680cb62
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910927"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte.

Vous pouvez rechercher un lieu d’intérêt de deux façons. La première consiste à utiliser un module de service pour effectuer une demande de recherche. La seconde consiste à effectuer une requête de recherche à l'[API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via l'[API de récupération](https://fetch.spec.whatwg.org/). Les deux méthodes sont décrites ci-dessous.

## <a name="make-a-search-request-via-service-module"></a>Demande de recherche via un module de service

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `TokenCredential` pour authentifier les requêtes HTTP auprès d’Azure Maps avec le jeton d’accès. Il transfère ensuite le `TokenCredential` à `atlas.service.MapsURL.newPipeline()` et crée une instance [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `searchURL` représente une URL vers les opérations de [recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps.

Le troisième bloc de code crée un objet source de données à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) et y ajoute les résultats de la recherche. Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte.  La couche de symboles est ensuite créée, la source de données y est ajoutée et l’ensemble est ensuite ajouté à la carte.

Le quatrième bloc de code utilise la méthode [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) dans le [module de service](how-to-use-services-module.md). Cela vous permet d’effectuer une recherche de texte de forme libre via [l’API REST de recherche approximative](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des points d’intérêt. L’API de recherche approximative peut gérer n’importe quelle combinaison d’entrées approximatives. Une collection de fonctionnalités GeoJSON déterminée par la réponse est ensuite extraite à l’aide de la méthode `geojson.getFeatures()` et ajoutée à la source de données. Les données sont alors automatiquement restituées sur la carte par le biais de la couche de symboles.

Le dernier bloc de code ajuste les limites d’appareil photo pour la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

La requête de recherche, la source de données, la couche de symboles et les limites d’appareil photo sont créés et définis au sein du [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que les résultats sont affichés après le chargement complet de la carte.


## <a name="make-a-search-request-via-fetch-api"></a>Requête de recherche via l'API de récupération

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map </a> (Afficher les résultats de recherche sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée une URL pour effectuer une requête de recherche. Il crée également deux tableaux pour stocker les limites et les repères des résultats de la recherche.

Le troisième bloc de code utilise l'[API de récupération](https://fetch.spec.whatwg.org/) pour demander à [l’API de recherche approximative Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) de rechercher les lieux d’intérêt. L’API de recherche floue peut gérer n’importe quelle combinaison d’entrées approximatives. Elle gère et analyse ensuite la réponse de recherche et ajoute les repères qui en résultent au tableau searchPins.

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
> [Obtenir des informations à partir d’une coordonnée](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)
