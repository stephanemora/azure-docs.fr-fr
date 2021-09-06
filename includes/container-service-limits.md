---
title: Fichier Include
description: Fichier Include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: d9566564c168e37477d8a0a8238c93dfb8be054d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336153"
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

> [!IMPORTANT]
> Nous recommandons aux clients d’activer le niveau Contrat SLA de durée de fonctionnement pour les charges de travail de production et de test de charge et de performances. Le niveau Contrat SLA de durée de fonctionnement permet la mise à l’échelle automatique du plan de contrôle Kubernetes et est requis pour prendre en charge les clusters avec des charges de travail de production dans [le Contrat SLA de durée de fonctionnement](../articles/aks/uptime-sla.md).

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
