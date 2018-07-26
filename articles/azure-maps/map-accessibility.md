---
title: Création d’une application accessible avec Azure Maps | Microsoft Docs
description: Comment créer une application accessible à l’aide d’Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867050"
---
# <a name="building-an-accessible-application"></a>Création d’une application accessible

Cet article vous explique comment créer une application de carte qui peut être utilisée par un lecteur d’écran.

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Créer une application accessible' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Créer une application accessible</a> d’Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

La carte est précréée avec quelques fonctionnalités d’accessibilité. Un utilisateur peut naviguer sur la carte à l’aide du clavier, et si un lecteur d’écran est utilisé, la carte informera l’utilisateur des modifications apportées à son état. Par exemple, l’utilisateur sera informé des nouvelles données de latitude, longitude, zoom et localité de la carte en cas de zoom ou de panoramique sur celle-ci. Toutes les informations supplémentaires placées sur la carte de la base doivent avoir des informations textuelles correspondantes pour les utilisateurs d’un lecteur d’écran. Pour ce faire, il faut utiliser la classe [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest). Dans l’exemple de recherche ci-dessus, une fenêtre contextuelle avec des informations textuelles est ajoutée à la carte pour chaque épingle placée sur la carte. La méthode « attach » de la classe [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) permet à la fenêtre contextuelle d’être visible par un lecteur d’écran sans afficher visuellement la fenêtre contextuelle sur la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la classe Popup et les méthodes utilisées dans cet article :

* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Consultez notre [page d’exemples de code](http://aka.ms/AzureMapsSamples) pour d’autres scénarios de mappage.
