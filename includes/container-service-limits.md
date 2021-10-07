---
title: fichier descriptif
description: Fichier include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 928c4669ec7b1a04874f760cc14234cfa874e99a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437888"
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

<br />

| Limites du plan de contrôle Kubernetes                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Niveau payant                                                                                  | Mise à l’échelle automatique en fonction de la charge                                                                                                                                                                                                        |
| Niveau gratuit                       | Ressources limitées avec une [limite des demandes en cours](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) fixée à 50 appels en mutation et 100 appels en lecture seule                                                                                                                                                                                                         |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
