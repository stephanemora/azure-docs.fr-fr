---
title: Afficher des informations sur une coordonnée avec Azure Maps | Microsoft Docs
description: Guide d’affichage des informations sur une adresse sur la carte au moment de la sélection d’une coordonnée par un utilisateur
author: jingjing-z
ms.author: jinzh
ms.date: 09/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bf44437f4d0b60a5d56c2be29418b7132346da2e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732292"
---
# <a name="get-information-from-a-coordinate"></a>Obtenir des informations à partir d’une coordonnée

Cet article explique comment effectuer une recherche d’adresses inversée qui affiche l’adresse d’un emplacement d’une fenêtre contextuelle sur lequel l’utilisateur a cliqué.

Deux méthodes permettent d’effectuer une recherche d’adresses inversée. La première consiste à interroger [l’API Azure Maps Reverse Address Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via un module de service. La seconde consiste à envoyer une requête [XMLHttpRequest](https://xhr.spec.whatwg.org/) à l’API pour rechercher une adresse. Les deux méthodes sont examinées ci-dessous.

## <a name="make-a-reverse-search-request-via-service-module"></a>Demande de recherche inversée via un module de service

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée (module de Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obtenir des informations à partir d’une coordonnée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

La ligne dans le deuxième bloc de code instancie un client de service.

Le troisième bloc de code modifie en pointeur le style du curseur de la souris.

Le quatrième bloc de code crée une fenêtre contextuelle à l’aide de la propriété [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le dernier bloc de code [ajoute un détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) pour les clics de souris. En cas de clic de souris, il crée une requête de recherche avec les coordonnées du point activé. Il utilise ensuite le point de terminaison [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) de la carte pour interroger l’adresse afin d’obtenir les coordonnées.

Pour une réponse correcte, il collecte l’adresse de l’emplacement activé et définit le contenu et la position de la fenêtre contextuelle via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Demande de recherche inversée via XMLHttpRequest

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Obtenir des informations à partir d’une coordonnée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code modifie en pointeur le style du curseur de la souris.

Le troisième bloc de code crée une fenêtre contextuelle à l’aide de la propriété [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le dernier bloc de code ajoute un détecteur d’événements associé aux clics de souris. À la suite d’un clic de souris, il transmet un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à l’[API de recherche d’adresse inversée d’Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Dans le cadre d’une réponse correcte, il collecte l’adresse de l’emplacement activé et définit le contenu et la position de la fenêtre contextuelle via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

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
