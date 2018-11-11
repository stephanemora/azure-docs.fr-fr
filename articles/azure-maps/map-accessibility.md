---
title: Création d’une application accessible avec Azure Maps | Microsoft Docs
description: Comment créer une application accessible à l’aide d’Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247226"
---
# <a name="building-an-accessible-application"></a>Création d’une application accessible

Cet article vous explique comment créer une application de carte qui peut être utilisée par un lecteur d’écran.

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Créer une application accessible' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Créer une application accessible</a> d’Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

La carte est précréée avec des fonctionnalités d’accessibilité. Un utilisateur peut naviguer sur la carte à l’aide du clavier. Si un lecteur d’écran est utilisé, la carte informera l’utilisateur des modifications apportées à son état.
Par exemple, les utilisateurs sont informés des modifications de la carte en cas de zoom ou de panoramique de celle-ci. Toutes les informations supplémentaires placées sur la carte de la base doivent avoir des informations textuelles correspondantes pour les utilisateurs d’un lecteur d’écran.

Pour ce faire, il faut utiliser la classe [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest). Dans l’exemple de recherche ci-dessus, une fenêtre contextuelle avec des informations textuelles est ajoutée à la carte pour chaque épingle placée sur la carte. La méthode [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) de la classe [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) permet à la fenêtre contextuelle d’être visible par un lecteur d’écran sans afficher visuellement la fenêtre contextuelle sur la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la classe Popup et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Découvrez d’autres exemples de code :

> [!div class="nextstepaction"]
> [Page des exemples de code](https://aka.ms/AzureMapsSamples)
