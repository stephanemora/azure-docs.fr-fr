---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67177437"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Rôles de travail ou web par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Points de terminaison d'entrée d'instance](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) par déploiement |25 |25 |
| [Points de terminaison d'entrée](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) par déploiement |25 |25 |
| [Points de terminaison internes](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) par déploiement |25 |25 |
| [Certificats de services hébergés](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) par déploiement |199 |199 |

<sup>1</sup>Chaque service cloud Azure avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Cette limite fait référence au nombre de rôles distincts, autrement dit à la configuration. Cette limite ne fait pas référence au nombre d’instances par rôle, autrement dit, à la mise à l’échelle.

