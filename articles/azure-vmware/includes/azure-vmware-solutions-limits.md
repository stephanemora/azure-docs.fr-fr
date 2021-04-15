---
title: Limites d’Azure VMware Solution
description: Limitations d’Azure VMware Solution.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582645"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Le tableau suivant décrit les limites maximales d’Azure VMware Solution.

| **Ressource** | **Limite** |
| :-- | :-- |
| Clusters par cloud privé | 12 |
| Nombre minimal de nœuds par cluster | 3 |
| Nombre maximal de nœuds par cluster | 16 |
| Nœuds par cloud privé | 96 |
| vCenter par cloud privé | 1  |
| Jumelages de sites HCX | 3 avec l’édition Advanced, 10 avec l’édition Enterprise |
| Centres de données défini par logiciel (SDDC) liés à ExpressRoute AVS max | 4 |
| Portspeed ExpressRoute AVS | 10 Gbits/s<br />La passerelle de réseau virtuel utilisée détermine la bande passante réelle. Pour plus d’informations, consultez [À propos des passerelles de réseau virtuel ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| IP publiques exposées via vWAN | 100 |
| Limites de capacité de vSAN | 75 % du total utilisable (garder 25 % disponible pour le SLA)  |

Pour les autres limites spécifiques à VMware, utilisez l’[outil de configuration maximale de VMware](https://configmax.vmware.com/).
