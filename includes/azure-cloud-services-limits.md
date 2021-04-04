---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838995"
---
| Ressource | Limite |
| --- | --- |
| [Rôles de travail ou web par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Points de terminaison d'entrée d'instance](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) par déploiement |25 |
| [Points de terminaison d'entrée](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) par déploiement |25 |
| [Points de terminaison internes](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) par déploiement |25 |
| [Certificats de services hébergés](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) par déploiement |199 |

<sup>1</sup>Chaque service cloud Azure avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Cette limite fait référence au nombre de rôles distincts, autrement dit à la configuration. Cette limite ne fait pas référence au nombre d’instances par rôle, autrement dit, à la mise à l’échelle.

