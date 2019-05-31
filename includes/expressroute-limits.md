---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9f7f9b1504533e0ec503e97c086c380da71671a0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299710"
---
| Ressource | Limite par défaut/maximale |
| --- | --- |
| Circuits ExpressRoute par abonnement |10 |
| Circuits ExpressRoute par région et par abonnement, avec Azure Resource Manager |10 |
| Nombre maximal d’itinéraires publiés pour l’homologation privée Azure avec ExpressRoute Standard |4 000 |
| Nombre maximal d’itinéraires publiés pour l’homologation privée Azure avec le module complémentaire ExpressRoute Premium |10 000 |
| Nombre maximal d’itinéraires publiés à partir de l’homologation privée Azure à partir de l’espace d’adressage de réseau virtuel pour une connexion ExpressRoute |200 |
| Nombre maximal d’itinéraires publiés pour l’homologation Microsoft avec ExpressRoute Standard |200 |
| Nombre maximal d’itinéraires publiés pour l’homologation Microsoft avec le module complémentaire ExpressRoute Premium |200 |
| Nombre maximal de circuits ExpressRoute lié au même réseau virtuel dans le même emplacement d’homologation |4 |
| Nombre maximal de circuits ExpressRoute liés au même réseau virtuel dans différents emplacements d’appairage |4 |
| Nombre de liens de réseau virtuel autorisés par circuit ExpressRoute |Consultez le [nombre de réseaux virtuels par circuit ExpressRoute](#vnetpercircuit) table.  |

#### <a name="vnetpercircuit"></a> Nombre de réseaux virtuels par circuit ExpressRoute
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
| 40 Gbits/s * |10 |100 |
| 100 Gbits/s * |10 |100 |

* Direct ExpressRoute uniquement
