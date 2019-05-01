---
title: Afficher des informations sur une coordonnée avec Azure Maps | Microsoft Docs
description: Guide d’affichage des informations sur une adresse sur la carte au moment de la sélection d’une coordonnée par un utilisateur
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769122"
---
# <a name="get-information-from-a-coordinate"></a>Obtenir des informations à partir d’une coordonnée

Cet article explique comment effectuer une recherche d’adresses inversée qui affiche l’adresse d’un emplacement d’une fenêtre contextuelle sur lequel l’utilisateur a cliqué.

Deux méthodes permettent d’effectuer une recherche d’adresses inversée. La première consiste à interroger [l’API Azure Maps Reverse Address Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via un module de service. L’autre consiste à utiliser le [API Fetch](https://fetch.spec.whatwg.org/) pour effectuer une demande à la [API de recherche Azure Maps inverser adresse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) à trouver une adresse. Les deux méthodes sont examinées ci-dessous.

## <a name="make-a-reverse-search-request-via-service-module"></a>Demande de recherche inversée via un module de service

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée (module de Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obtenir des informations à partir d’une coordonnée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un `SubscriptionKeyCredentialPolicy` pour authentifier les demandes HTTP vers Azure Maps avec la clé d’abonnement. Le `atlas.service.MapsURL.newPipeline()` prend le `SubscriptionKeyCredential` stratégie et crée un [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instance. `searchURL` représente une URL vers les opérations de [recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps.

Le troisième bloc de code met à jour le style du curseur de souris à un pointeur et crée un [contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objet. Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le quatrième bloc de code ajoute un clic de souris [écouteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Déclenchement de, il crée une requête de recherche avec les coordonnées du point cliqué dessu. Il utilise ensuite le module service [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) méthode pour interroger le [API obtention du Search adresse inverser](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pour l’adresse des coordonnées. Une collection de fonctionnalité GeoJSON à partir de la réponse est ensuite extraites à l’aide de la `geojson.getFeatures()` (méthode).

Le cinquième bloc de code définit le contenu de la fenêtre contextuelle HTML à afficher l’adresse de réponse pour la position des coordonnées utilisateur a cliqué dessue.

Le changement de curseur, un objet de fenêtre contextuelle et l’événement de clic sont tous créés dans le [détecteur d’événements de chargement](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que la carte est entièrement chargée avant de pouvoir récupérer les informations de coordonnées.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Effectuer une demande de recherche inversée via l’API d’extraction

Cliquez sur la carte pour effectuer une requête de Géocode inverse pour cet emplacement à l’aide de fetch.

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Obtenir des informations à partir d’une coordonnée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de mappage et définit le mécanisme d’authentification à utiliser la clé d’abonnement. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code change le style du curseur de la souris en pointeur et en objet de [fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le troisième bloc de code ajoute un détecteur d’événements pour les clics de souris. Lors d’un clic de souris, il utilise le [API Fetch](https://fetch.spec.whatwg.org/) pour interroger le [API de recherche Azure Maps inverser adresse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pour l’adresse de l’utilisateur a cliqué dessus coordonnées. Pour une réponse correcte, il collecte l’adresse de l’emplacement activé, et définit le contenu et la position de la fenêtre contextuelle via la fonction [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) de la classe popup.

Le changement de curseur, un objet de fenêtre contextuelle et l’événement de clic sont tous créés dans le [détecteur d’événements de chargement](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la carte pour garantir que la carte est entièrement chargée avant de pouvoir récupérer les informations de coordonnées.

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
