---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 654176074b99af060ed35297369b8f7eecb7e283
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453980"
---
## <a name="simultaneous-use-of-private-endpoints-and-virtual-network-service-endpoints"></a>Utilisation simultanée de points de terminaison privés et de points de terminaison de service du Réseau virtuel

Les [points de terminaison privés](../speech-services-private-link.md) et les [points de terminaison de service du Réseau virtuel](../speech-service-vnet-service-endpoint.md) peuvent être utilisés pour accéder simultanément à la même ressource Speech. Pour activer cette utilisation simultanée, vous devez utiliser l’option **Réseaux et points de terminaison privés sélectionnés** dans les paramètres réseau de la ressource Speech dans le portail Azure. Toutes les autres options ne sont pas prises en charge pour ce scénario.
