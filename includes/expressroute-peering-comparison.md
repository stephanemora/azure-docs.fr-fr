---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750526"
---
|  | **Peering privé** | **Peering Microsoft** |  **Peering public** (déprécié pour les nouveaux circuits) |
| --- | --- | --- | --- |
| **Nb max. de préfixes pris en charge par peering** |4 000 par défaut, 10 000 avec ExpressRoute Premium |200 |200 |
| **Plages d’adresses IP prises en charge** |Toute adresse IP valide au sein de votre réseau étendu. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |Adresses IP publiques détenues par vous ou par votre fournisseur de connectivité. |
| **Exigences en matière de numéros AS** |Numéros AS publics et privés Vous devez être propriétaire du numéro AS public si vous choisissez d’en utiliser un. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |Numéros AS publics et privés Cependant, vous devez prouver la propriété des adresses IP publiques. |
| **Protocoles IP pris en charge**| IPv4, IPv6 (version préliminaire) |  IPv4, IPv6 | IPv4 |
| **Adresses IP de l’interface de routage** |RFC1918 et adresses IP publiques |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |Adresses IP publiques enregistrées auprès de vous dans les registres de routage. |
| **Prise en charge du hachage MD5** |Oui |Oui |Oui |