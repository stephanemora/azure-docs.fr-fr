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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177426"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nœuds maximum par cluster | 100 |
| Nombre maximal de pods par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Nombre maximal de pods par nœud : [Mise en réseau avancée] [ advanced-networking] avec Azure Container Networking Interface | Déploiement de l’interface Azure CLI : 30<sup>1</sup><br />Modèle Azure Resource Manager : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup> Quand vous déployez un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager, cette valeur est configurable jusqu’à 250 pods par nœud. Vous ne pouvez pas configurer le nombre maximum de pods par nœud après avoir déployé un cluster AKS ou si vous avez déployé un cluster à l’aide du Portail Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
