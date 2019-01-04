---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886248"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Rôles de travail/web par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Points de terminaison d'entrée d'instance](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) par déploiement |25 |25 |
| [Points de terminaison d'entrée](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) par déploiement |25 |25 |
| [Points de terminaison internes](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) par déploiement |25 |25 |
| [Certificats de services hébergés](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) par déploiement |199 |199 |

<sup>1</sup>Chaque service cloud avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Notez aussi que cette limite s'applique au nombre de rôles distincts (configuration) et non au nombre d'instances par rôle (extensibilité).

