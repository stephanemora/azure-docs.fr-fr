---
title: Limites d’Azure VMware Solution
description: Limitations d’Azure VMware Solution.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: ce0f346c66c7eb982f0891b82e710affdcc956a8
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109719096"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

Le tableau suivant décrit les limites maximales d’Azure VMware Solution.

| **Ressource** | **Limite** |
| :-- | :-- |
| Clusters par cloud privé | 12 |
| Nombre minimal de nœuds par cluster | 3 |
| Nombre maximal de nœuds par cluster | 16 |
| Nœuds par cloud privé | 96 |
| vCenter par cloud privé | 1  |
| Jumelages de sites HCX | 3 avec l’édition Advanced, 10 avec l’édition Enterprise |
| Nombre maximal de clouds privés liés ExpressRoute AVS | 4<br />La passerelle de réseau virtuel utilisée détermine le nombre maximal de clouds privés liés réels.  Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| Portspeed ExpressRoute AVS | 10 Gbits/s<br />La passerelle de réseau virtuel utilisée détermine la bande passante réelle. Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| IP publiques exposées via vWAN | 100 |
| Limites de capacité de vSAN | 75 % du total utilisable (garder 25 % disponible pour le SLA)  |

Pour les autres limites spécifiques à VMware, utilisez l’[outil de configuration maximale de VMware](https://configmax.vmware.com/).
