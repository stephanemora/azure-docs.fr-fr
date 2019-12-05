---
title: Fichier Include
description: Fichier Include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485503"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nombre maximal de nœuds par cluster avec les groupes à haute disponibilité de machines virtuelles et la référence SKU Standard Load Balancer  | 100 |
| Nombre maximal de nœuds par cluster avec Virtual Machine Scale Sets et la référence [SKU Standard Load Balancer][standard-load-balancer] | 800 (100 nœuds par [pool de nœuds][node-pool]) |
| Nombre maximal de pods par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Nombre maximal de pods par nœud : [mise en réseau avancée][advanced-networking] avec Azure Container Networking Interface | Déploiement de l’interface Azure CLI : 30<sup>1</sup><br />Modèle Azure Resource Manager : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup> Quand vous déployez un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager, cette valeur est configurable jusqu’à 250 pods par nœud. Vous ne pouvez pas configurer le nombre maximum de pods par nœud après avoir déployé un cluster AKS ou si vous avez déployé un cluster à l’aide du Portail Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
