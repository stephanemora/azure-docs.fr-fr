---
title: Afficher des informations sur une coordonnée avec Azure Maps | Microsoft Docs
description: Guide d’affichage des informations sur une adresse sur la carte au moment de la sélection d’une coordonnée par un utilisateur
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b640346b0d6f490457e1e82a65c0d3f373d658d3
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42145511"
---
# <a name="get-information-from-a-coordinate"></a>Obtenir des informations à partir d’une coordonnée

Cet article vous explique la procédure de recherche inversée d’une adresse, et d’affichage dans une fenêtre contextuelle de l’adresse associée à un emplacement à la suite d’un clic. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Obtenir des informations à partir d’une coordonnée' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Obtenir des informations à partir d’une coordonnée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code modifie en pointeur le style du curseur de la souris.

Le troisième code de bloc crée une fenêtre contextuelle. Pour plus d’instructions, consultez la section relative à l’[ajout d’une fenêtre contextuelle sur la carte](./map-add-popup.md).

Le dernier bloc de code ajoute un détecteur d’événements associé aux clics de souris. À la suite d’un clic de souris, il transmet un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à l’[API de recherche d’adresse inversée d’Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Dans le cadre d’une réponse correcte, il collecte l’adresse de l’emplacement activé et définit le contenu et la position de la fenêtre contextuelle via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Reverse address search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Afficher des directions de A à B](./map-route.md)
* [Afficher le trafic](./map-show-traffic.md)
