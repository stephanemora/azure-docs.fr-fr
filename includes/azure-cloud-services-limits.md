---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238435"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Rôles Web ou de travail par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instance d’entrée des points de terminaison](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) par déploiement |25 |25 |
| [Entrée des points de terminaison](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) par déploiement |25 |25 |
| [Points de terminaison internes](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) par déploiement |25 |25 |
| [Certificats de services hébergés](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) par déploiement |199 |199 |

<sup>1</sup>chaque Service de Cloud Azure avec des rôles web ou de travail peut avoir deux déploiements, un pour la production et l’autre pour la mise en lots. Cette limite s’applique au nombre de rôles distincts, autrement dit, de configuration. Cette limite ne fait pas référence au nombre d’instances par rôle, autrement dit, mise à l’échelle.

