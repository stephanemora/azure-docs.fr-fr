---
title: Afficher des informations à partir d’une coordonnée sur une carte | Microsoft Azure Maps
description: Découvrez comment afficher des informations sur une adresse sur la carte quand un utilisateur sélectionne une coordonnée.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0b1ab7b2c233eb1e6e231b0ae7935b6c24363948
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988530"
---
# <a name="get-information-from-a-coordinate"></a>Obtenir des informations à partir d’une coordonnée

Cet article explique comment effectuer une recherche d’adresses inversée qui affiche l’adresse d’un emplacement d’une fenêtre contextuelle sur lequel l’utilisateur a cliqué.

Deux méthodes permettent d’effectuer une recherche d’adresses inversée. La première consiste à interroger [l’API Azure Maps Reverse Address Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via un module de service. La seconde consiste à utiliser l'[API de récupération](https://fetch.spec.whatwg.org/) pour demander à l'[API de recherche d'adresses inversée Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) de rechercher une adresse. Les deux méthodes sont examinées ci-dessous.

## <a name="make-a-reverse-search-request-via-service-module"></a>Demande de recherche inversée via un module de service

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée (module de Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obtenir des informations à partir d’une coordonnée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `TokenCredential` pour authentifier les requêtes HTTP auprès d’Azure Maps avec le jeton d’accès. Il transfère ensuite le `TokenCredential` à `atlas.service.MapsURL.newPipeline()` et crée une instance [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `searchURL` représente une URL vers les opérations de [recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps.

Le troisième bloc de code change le style du curseur de la souris en pointeur et crée un objet [fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le quatrième bloc de code ajoute un [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) clic de souris. Lorsqu'il est déclenché, il crée une requête de recherche avec les coordonnées du point activé. Il utilise ensuite la méthode [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) pour interroger l’[API Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) et obtenir l’adresse des coordonnées. Une collection d’entités GeoJSON est alors extraite de la réponse à l’aide de la méthode `geojson.getFeatures()`.

Le cinquième bloc de code définit le contenu de la fenêtre contextuelle HTML pour afficher l’adresse de réponse correspondant à la position des coordonnées sur lesquelles vous avez cliqué.

Le changement de curseur, un objet de fenêtre contextuelle et l’événement de clic sont tous créés dans le [détecteur d’événements de chargement](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que la carte est entièrement chargée avant la récupération des informations de coordonnées.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Requête de recherche inversée via l'API de récupération

Cliquez sur la carte pour effectuer une requête de géocode inversé pour cette position à l'aide de récupérer (fetch).

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Obtenir des informations à partir d’une coordonnée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte et définit le mécanisme d’authentification pour utiliser le jeton d’accès. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code modifie en pointeur le style du curseur de la souris. Il instancie un objet de [fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le troisième bloc de code ajoute un détecteur d’événements pour les clics de souris. À la suite d’un clic de souris, il utilise l’[API Fetch](https://fetch.spec.whatwg.org/) pour interroger l’[API Azure Maps Reverse Address Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) et obtenir l’adresse des coordonnées activées. Pour une réponse correcte, il collecte l’adresse de l’emplacement sur lequel l’utilisateur a cliqué. Il définit le contenu et la position de la fenêtre contextuelle à l’aide de la fonction [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) de la classe de fenêtre contextuelle.

Le changement de curseur, un objet de fenêtre contextuelle et l’événement de clic sont tous créés dans le [détecteur d’événements de chargement](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que la carte est entièrement chargée avant la récupération des informations de coordonnées.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Afficher des directions de A à B](./map-route.md)

> [!div class="nextstepaction"]
> [Afficher le trafic](./map-show-traffic.md)
