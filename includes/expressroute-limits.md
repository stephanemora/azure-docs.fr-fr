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
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755716"
---
#### <a name="expressroute-limits"></a>Limites d’ExpressRoute
Les limites suivantes s’appliquent aux ressources ExpressRoute par abonnement.

| Ressource | Limite par défaut |
| --- | --- |
| Circuits ExpressRoute par abonnement |10 |
| Circuits ExpressRoute par région par abonnement pour ARM |10 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec ExpressRoute Standard |4 000 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec le module complémentaire ExpressRoute Premium |10 000 |
| Nombre maximal d’itinéraires pour l’homologation publique Azure avec ExpressRoute Standard |200 |
| Nombre maximal d’itinéraires pour l’homologation publique Azure avec le module complémentaire ExpressRoute Premium |200 |
| Nombre maximal d’itinéraires pour l’homologation Microsoft Azure avec ExpressRoute Standard |200 |
| Nombre maximal d’itinéraires pour l’homologation Microsoft Azure avec le module complémentaire ExpressRoute Premium |200 |
| Nombre maximal de circuits ExpressRoute liés au même réseau virtuel dans différents emplacements d’appairage |4 |
| Nombre de liens de réseau virtuel autorisés par circuit ExpressRoute |voir le tableau ci-dessous |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Nombre de réseaux virtuels par circuit ExpressRoute
| **Taille du circuit** | **Nombre de liens de réseau virtuel de la configuration pour la version standard** | **Nombre de liens de réseau virtuel avec le module complémentaire Premium** |
| --- | --- | --- |
| 50 Mbits/s |10 |20 |
| 100 Mbits/s |10 |25 |
| 200 Mbits/s |10 |25 |
| 500 Mbits/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbits/s |10 |100 |

