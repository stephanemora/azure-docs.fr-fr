---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279614"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Rôles de travail/web par déploiement](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Points de terminaison d'entrée d'instance](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) par déploiement |25 |25 |
| [Points de terminaison d'entrée](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) par déploiement |25 |25 |
| [Points de terminaison internes](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) par déploiement |25 |25 |

<sup>1</sup>Chaque service cloud avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Notez aussi que cette limite s'applique au nombre de rôles distincts (configuration) et non au nombre d'instances par rôle (extensibilité).

