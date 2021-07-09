---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 8a1253e7ac88d2fe8b557c48dd846b48de59fba3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059148"
---
## <a name="simultaneous-use-of-private-endpoints-and-vnet-service-endpoints"></a>Utilisation simultanée de points de terminaison privés et de points de terminaison de service de réseau virtuel

Les [points de terminaison privés](../speech-services-private-link.md) et les [points de terminaison de service de réseau virtuel](../speech-service-vnet-service-endpoint.md) peuvent être utilisés pour accéder simultanément à la même ressource Speech. Toutefois, pour activer simultanément les points de terminaison privés et les points de terminaison de service de réseau virtuel, vous devez utiliser l’option **Réseaux et points de terminaison privés sélectionnés** dans les paramètres réseau de la ressource Speech dans le portail Azure. Toutes les autres options ne sont pas prises en charge pour ce scénario.
