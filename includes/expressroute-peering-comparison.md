---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 47e3d4e86e5288bb9a75d9d2b9a0b053ac50fc8e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242274"
---
|  | **Peering privé** | **Peering Microsoft** |  **Peering public** (déprécié pour les nouveaux circuits) |
| --- | --- | --- | --- |
| **Nb max. de préfixes pris en charge par peering** |4 000 par défaut, 10 000 avec ExpressRoute Premium |200 |200 |
| **Plages d’adresses IP prises en charge** |Toute adresse IP valide au sein de votre réseau étendu. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |
| **Exigences en matière de numéros AS** |Numéros AS publics et privés Vous devez être propriétaire du numéro AS public si vous choisissez d’en utiliser un. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |
| **Protocoles IP pris en charge**| IPv4, IPv6 |  IPv4, IPv6 | IPv4 |
| **Adresses IP de l’interface de routage** |RFC1918 et adresses IP publiques |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |
| **Prise en charge du hachage MD5** |Oui |Oui |Oui |
