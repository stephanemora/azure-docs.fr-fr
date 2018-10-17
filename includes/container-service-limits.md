---
title: Fichier Include
description: Fichier Include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874148"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nœuds maximum par cluster | 100 |
| Pods maximum par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Pods maximum par nœud : [mise en réseau avancée][advanced-networking] avec Azure CNI | Déploiement de l’interface de ligne de commande Azure : 30<sup>1</sup><br />Modèle Resource Manager : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup> Cette valeur est configurable au moment du déploiement d’un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
