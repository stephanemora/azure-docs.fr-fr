---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553591"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Rôles Web ou de travail par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instance d’entrée des points de terminaison](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) par déploiement |25 |25 |
| [Entrée des points de terminaison](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) par déploiement |25 |25 |
| [Points de terminaison internes](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) par déploiement |25 |25 |
| [Certificats de services hébergés](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) par déploiement |199 |199 |

<sup>1</sup>chaque Service de Cloud Azure avec des rôles web ou de travail peut avoir deux déploiements, un pour la production et l’autre pour la mise en lots. Cette limite s’applique au nombre de rôles distincts, autrement dit, de configuration. Cette limite ne fait pas référence au nombre d’instances par rôle, autrement dit, mise à l’échelle.

