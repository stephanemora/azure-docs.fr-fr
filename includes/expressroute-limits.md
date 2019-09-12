---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67622096"
---
| Ressource | Limite par défaut/maximale |
| --- | --- |
| Circuits ExpressRoute par abonnement |10 |
| Circuits ExpressRoute par région par abonnement, avec Azure Resource Manager |10 |
| Nombre maximal d’itinéraires publiés vers l’homologation privée Azure avec ExpressRoute Standard |4 000 |
| Nombre maximal d’itinéraires publiés vers l’homologation privée Azure avec le module complémentaire ExpressRoute Premium |10 000 |
| Nombre maximal d'itinéraires publiés depuis l’homologation privée Azure à partir de l’espace d’adressage de réseau virtuel pour une connexion ExpressRoute |200 |
| Nombre maximal d’itinéraires publiés vers l’homologation Microsoft avec ExpressRoute Standard |200 |
| Nombre maximal d’itinéraires publiés vers l’homologation Microsoft avec le module complémentaire ExpressRoute Premium |200 |
| Nombre maximal de circuits ExpressRoute liés au même réseau virtuel dans le même emplacement d’appairage |4 |
| Nombre maximal de circuits ExpressRoute liés au même réseau virtuel dans différents emplacements d’appairage |4 |
| Nombre de liens de réseau virtuel autorisés par circuit ExpressRoute |Consultez le tableau [Nombre de réseaux virtuels par circuit ExpressRoute](#vnetpercircuit).  |

#### <a name="vnetpercircuit"></a> Nombre de réseaux virtuels par circuit ExpressRoute
| **Taille du circuit** | **Nombre de liens de réseau virtuel pour la version standard** | **Nombre de liens de réseau virtuel avec le module complémentaire Premium** |
| --- | --- | --- |
| 50 Mbits/s |10 |20 |
| 100 Mbits/s |10 |25 |
| 200 Mbits/s |10 |25 |
| 500 Mbits/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbits/s |10 |100 |
| 40 Gbits/s* |10 |100 |
| 100 Gbits/s* |10 |100 |

**ExpressRoute Direct 100 Gbits/s uniquement*
