---
title: Fichier include
description: Fichier include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799805"
---
| Ressource                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Clusters maximum par abonnement                                                                                  | 5 000                                                                                                                                                                                                        |
| Nombre maximal de nœuds par cluster avec les groupes à haute disponibilité de machines virtuelles et la référence SKU Standard Load Balancer                       | 100                                                                                                                                                                                                         |
| Nombre maximal de nœuds par cluster avec Virtual Machine Scale Sets et la référence [SKU Standard Load Balancer][standard-load-balancer] | 1 000 (dans tous les [pools de nœuds][node-pool])                                            |
| Nombre maximal de pools de nœuds par cluster                                                                                     | 100                                                                                  |
| Pods maximum par nœud :  : [réseau de base][basic-networking] avec Kubenet                                           | Maximum : 250 <br /> Valeur Azure CLI par défaut : 110 <br /> Valeur par défaut du modèle Azure Resource Manager : 110 <br /> Valeur par défaut du déploiement sur le portail Azure : 30          |
| Pods maximum par nœud : [réseau avancé][advanced-networking] avec Azure Container Networking Interface        | Maximum : 250 <br /> Valeur par défaut : 30                                                      |
| Module complémentaire AKS OSM (Open Service Mesh) AKS (préversion)                                                                          | Version du cluster Kubernetes : 1.19+<sup>1</sup><br />Contrôleurs OSM par cluster : 1<sup>1</sup><br />Nombre de pods par contrôleur OSM : 500<sup>1</sup><br />Comptes de service Kubernetes managés par OSM : 50<sup>1</sup> |

<sup>1</sup> Le module complémentaire OSM pour AKS est en préversion et fera l’objet d’améliorations supplémentaires avant sa mise en disponibilité générale. Pendant la phase de préversion, nous vous recommandons de ne pas dépasser les limites indiquées.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
