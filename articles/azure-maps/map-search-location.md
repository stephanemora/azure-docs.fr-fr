---
title: Afficher les résultats de recherche avec Azure Maps | Microsoft Docs
description: Comment effectuer une demande de recherche avec Azure Maps, puis afficher les résultats sur une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768918"
---
# <a name="show-search-results-on-the-map"></a>Afficher les résultats de recherche sur la carte

Cet article explique comment rechercher des lieux d’intérêt et afficher les résultats de la recherche sur la carte.

Vous pouvez rechercher un lieu d’intérêt de deux façons. La première consiste à utiliser un module de service pour effectuer une demande de recherche. L’autre consiste à effectuer une requête de recherche pour [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via la [API Fetch](https://fetch.spec.whatwg.org/). Les deux méthodes sont décrites ci-dessous.

## <a name="make-a-search-request-via-service-module"></a>Demande de recherche via un module de service

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte (module de service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `SubscriptionKeyCredentialPolicy` pour authentifier les demandes HTTP vers Azure Maps avec la clé d’abonnement. Le `atlas.service.MapsURL.newPipeline()` prend le `SubscriptionKeyCredential` stratégie et crée un [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instance. `searchURL` représente une URL vers les opérations de [recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps.

Le troisième bloc de code crée un objet source de données à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) et y ajoute les résultats de la recherche. Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte.  La couche de symboles est ensuite créée, la source de données y est ajoutée et l’ensemble est ensuite ajouté à la carte.

Le quatrième bloc de code utilise le [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) méthode dans le [module service](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Il vous permet d’effectuer une recherche de texte de forme libre via la [obtenir les recherche floue rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher des points d’intérêt. L’API Get recherche floue peut gérer n’importe quelle combinaison d’entrées floues. Une collection de fonctionnalités GeoJSON déterminée par la réponse est ensuite extraite à l’aide de la méthode `geojson.getFeatures()` et ajoutée à la source de données. Les données sont alors automatiquement restituées sur la carte par le biais de la couche de symboles.

Le dernier bloc de code ajuste les limites d’appareil photo pour la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte.

Demande de la recherche, la source de données et couche de symbole et les limites de l’appareil photo sont créés et ensemble au sein de la carte du prêt [écouteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pour vous assurer que les résultats sont affichés après charge complet de la carte.


## <a name="make-a-search-request-via-fetch-api"></a>Effectuer une demande de recherche via une API d’extraction

<iframe height='500' scrolling='no' title='Afficher les résultats de la recherche sur une carte' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map </a> (Afficher les résultats de recherche sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée une URL pour effectuer une demande de recherche. Il crée également deux tableaux pour stocker des limites et des codes confidentiels pour les résultats de la recherche.

Le troisième bloc de code utilise le [API Fetch](https://fetch.spec.whatwg.org/) pour effectuer une demande à [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pour rechercher les points d’intérêt. L’API de recherche floue peut gérer n’importe quelle combinaison d’entrées approximatives. Puis, il gère et analyse de la réponse de recherche et ajoute les broches de résultat au tableau searchPins.

Le quatrième bloc de code crée un objet source de données à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) et y ajoute les résultats de la recherche. Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. La couche de symboles est ensuite créée, la source de données y est ajoutée et l’ensemble est ensuite ajouté à la carte.

Le dernier bloc de code crée un objet [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) à l’aide du tableau de résultats, puis ajuste les limites de l’appareil photo pour la carte à l’aide de la propriété [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) de la carte. Il restitue ensuite les broches du résultat.

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
