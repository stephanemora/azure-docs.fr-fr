---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ee987448f9f6d95e6f97317bec2f696e734cba20
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632911"
---
#### <a name="expressroute-limits"></a>Limites d’ExpressRoute
Les limites suivantes s’appliquent aux ressources Azure ExpressRoute par abonnement.

| Ressource | Limite par défaut/maximale |
| --- | --- |
| Circuits ExpressRoute par abonnement |10 |
| Circuits ExpressRoute par région et par abonnement, avec Azure Resource Manager |10 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec ExpressRoute Standard |4 000 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec le module complémentaire ExpressRoute Premium |10 000 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure à partir de l’espace d’adressage de réseau virtuel pour une connexion ExpressRoute |200 | 
| Nombre maximal d’itinéraires pour l’homologation de Microsoft Azure avec ExpressRoute Standard |200 |
| Nombre maximal d’itinéraires pour l’homologation de Microsoft Azure avec le module complémentaire ExpressRoute Premium |200 |
| Nombre maximal de circuits ExpressRoute liés au même réseau virtuel dans différents emplacements d’appairage |4 |
| Nombre de liens de réseau virtuel autorisés par circuit ExpressRoute |Consultez le tableau suivant. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Nombre de réseaux virtuels par circuit ExpressRoute
| **Taille du circuit** | **Nombre de liens de réseau virtuel pour l’Édition Standard** | **Nombre de liens de réseau virtuel avec le module complémentaire Premium** |
| --- | --- | --- |
| 50 Mbits/s |10 |20 |
| 100 Mbits/s |10 |25 |
| 200 Mbits/s |10 |25 |
| 500 Mbits/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbits/s |10 |100 |

