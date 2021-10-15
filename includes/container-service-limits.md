---
title: Fichier include
description: Fichier include
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 17a98a50f4c6e6d0723b62f1ea402c78f653bf4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378088"
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

<sup>1</sup> Le module complémentaire OSM pour AKS est en préversion et fera l’objet d’améliorations supplémentaires avant sa mise en disponibilité générale. Pendant la phase de préversion, nous vous recommandons de ne pas dépasser les limites indiquées.<br />

<br />

| Niveau de service du plan de contrôle Kubernetes | Limite |  
| -------------- | :--------------------------------------------- |
| Niveau payant      | Mise à l’échelle automatique en fonction de la charge     |
| Niveau Gratuit      | Ressources limitées avec une [limite des demandes en cours](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) fixée à 50 appels en mutation et 100 appels en lecture seule   |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
