---
title: Afficher des informations sur une coordonnée avec Azure Maps | Microsoft Docs
description: Guide d’affichage des informations sur une adresse sur la carte au moment de la sélection d’une coordonnée par un utilisateur
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667479"
---
# <a name="get-information-from-a-coordinate"></a>Obtenir des informations à partir d’une coordonnée

Cet article vous explique la procédure de recherche inversée d’une adresse, et d’affichage dans une fenêtre contextuelle de l’adresse associée à un emplacement à la suite d’un clic.

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée (module de Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obtenir des informations à partir d’une coordonnée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

La ligne dans le deuxième bloc de code instancie un client de service.

Le troisième bloc de code modifie en pointeur le style du curseur de la souris.

Le quatrième bloc de code crée une fenêtre contextuelle. Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le dernier bloc de code ajoute un écouteur d’événement pour les clics de souris. En cas de clic de souris, il crée une requête de recherche avec les coordonnées du point activé. Il utilise ensuite le point de terminaison [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) de la carte pour interroger l’adresse afin d’obtenir les coordonnées.

Pour une réponse correcte, il collecte l’adresse de l’emplacement activé et définit le contenu et la position de la fenêtre contextuelle via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Reverse address search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :
* [Afficher des directions de A à B](./map-route.md)
* [Afficher le trafic](./map-show-traffic.md)
