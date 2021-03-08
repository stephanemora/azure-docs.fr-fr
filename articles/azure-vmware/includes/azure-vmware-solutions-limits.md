---
title: Limites d’Azure VMware Solution
description: Limitations d’Azure VMware Solution.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193918"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Le tableau suivant décrit les limites maximales d’Azure VMware Solution.

| **Ressource** | **Limite** |
| :-- | :-- |
| Clusters par cloud privé | 4 |
| Nombre minimal de nœuds par cluster | 3 |
| Nombre maximal de nœuds par cluster | 16 |
| Nœuds par cloud privé | 64 |
| vCenter par cloud privé | 1  |
| Jumelages de sites HCX | 3 avec l’édition Advanced, 10 avec l’édition Enterprise |
| Centres de données défini par logiciel (SDDC) liés à ExpressRoute AVS max | 4 |
| Portspeed ExpressRoute AVS | 10 Gbits/s | 
| IP publiques exposées via vWAN | 100 |
| Limites de capacité de vSAN | 75 % du total utilisable (garder 25 % disponible pour le SLA)  |

Pour les autres limites spécifiques à VMware, utilisez l’[outil de configuration maximale de VMware](https://configmax.vmware.com/).
