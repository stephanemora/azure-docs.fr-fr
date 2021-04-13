---
title: Fichier include
description: Fichier include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081071"
---
| Ressource                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Clusters maximum par abonnement                                                                                  | 1 000                                                                                                                                                                                                        |
| Nombre maximal de nœuds par cluster avec les groupes à haute disponibilité de machines virtuelles et la référence SKU Standard Load Balancer                       | 100                                                                                                                                                                                                         |
| Nombre maximal de nœuds par cluster avec Virtual Machine Scale Sets et la référence [SKU Standard Load Balancer][standard-load-balancer] | 1 000 (100 nœuds par [pool de nœuds][node-pool])                                                                                                                                                                 |
| Pods maximum par nœud :  : [réseau de base][basic-networking] avec Kubenet                                           | 110                                                                                                                                                                                                         |
| Pods maximum par nœud : [réseau avancé][advanced-networking] avec Azure Container Networking Interface        | Déploiement de l’interface de ligne de commande Azure : 30<sup>1</sup><br />Modèle Azure Resource Manager : 30<sup>1</sup><br />Déploiement du portail : 30                                                                                        |
| Module complémentaire AKS OSM (Open Service Mesh) AKS (préversion)                                                                          | Version du cluster Kubernetes : 1.19+<sup>2</sup><br />Contrôleurs OSM par cluster : 1<sup>2</sup><br />Nombre de pods par contrôleur OSM : 500<sup>2</sup><br />Comptes de service Kubernetes managés par OSM : 50<sup>2</sup> |

<sup>1</sup> Quand vous déployez un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager, cette valeur est configurable jusqu’à 250 pods par nœud. Vous ne pouvez pas configurer le nombre maximum de pods par nœud après avoir déployé un cluster AKS ou si vous avez déployé un cluster à l’aide du Portail Azure.<br />

<sup>2</sup> Le module complémentaire OSM pour AKS est en préversion et fera l’objet d’améliorations supplémentaires avant sa mise en disponibilité générale. Pendant la phase de préversion, nous vous recommandons de ne pas dépasser les limites indiquées.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
