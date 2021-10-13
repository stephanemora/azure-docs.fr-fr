---
title: Limites d’Azure VMware Solution
description: Limitations d’Azure VMware Solution.
ms.topic: include
ms.date: 09/02/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 7a8479a5262c1db61dbb03558632ebebac1a6983
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638628"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

Le tableau suivant décrit les limites maximales d’Azure VMware Solution.

| **Ressource** | **Limite** |
| :-- | :-- |
| Clusters par cloud privé | 12 |
| Nombre minimal d’hôtes par cluster | 3 |
| Nombre maximal d’hôtes par cluster | 16 |
| hôtes par cloud privé | 96 |
| vCenter par cloud privé | 1  |
| Jumelages de sites HCX | 25 (toute édition) |
| Nombre maximal de clouds privés liés Azure VMware Solution ExpressRoute | 4<br />La passerelle de réseau virtuel utilisée détermine le nombre maximal de clouds privés liés réels.  Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| Vitesse de port Azure VMware Solution ExpressRoute | 10 Gbits/s<br />La passerelle de réseau virtuel utilisée détermine la bande passante réelle. Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| IP publiques exposées via vWAN | 100 |
| Limites de capacité de vSAN | 75 % du total utilisable (garder 25 % disponible pour le SLA)  |

Pour les autres limites spécifiques à VMware, utilisez l’[outil de configuration maximale de VMware !](https://configmax.vmware.com/).
