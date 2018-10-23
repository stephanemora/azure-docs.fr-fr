---
title: Fichier Include
description: Fichier Include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400355"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nœuds maximum par cluster | 100 |
| Pods maximum par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Pods maximum par nœud : [mise en réseau avancée][advanced-networking] avec Azure CNI | Déploiement de l’interface de ligne de commande Azure : 30<sup>1</sup><br />Modèle Resource Manager : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup> Quand vous déployez un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager, cette valeur est configurable jusqu’à **110 pods par nœud**. Vous ne pouvez pas configurer le nombre maximum de pods par nœud après avoir déployé un cluster AKS ou si vous avez déployé un cluster à l’aide du Portail Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
