---
title: Ajouter du code HTML personnalisé dans Azure Maps | Microsoft Docs
description: Guide d’ajout de code HTML personnalisé à une carte JavaScript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: df50774e1bdca53034d4856f38a6133fe8e89855
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="add-custom-html-to-the-map"></a>Ajouter du code HTML personnalisé à une carte

Cet article vous explique comment ajouter du code HTML personnalisé, par exemple un fichier d’image, à la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='466' scrolling='no' title='Ajouter du code HTML personnalisé à une carte - Fichier PNG' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add custom html to a map - png (Ajouter du code HTML personnalisé à une carte - Fichier PNG)</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un élément HTML à partir d’une image.

Le dernier bloc de code utilise la fonction [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) de la classe map pour ajouter l’image à la position spécifiée de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
