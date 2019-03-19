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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554715"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nombre maximal de nœuds par cluster | 100 |
| Pods maximum par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Pods maximum par nœud : [Avancées de mise en réseau] [ advanced-networking] avec l’Interface de mise en réseau de conteneur Azure | Déploiement de l’interface Azure CLI : 30<sup>1</sup><br />Modèle de gestionnaire de ressources Azure : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup>lorsque vous déployez un cluster Azure Kubernetes Service (AKS) avec l’interface CLI ou un modèle Resource Manager, cette valeur est configurable jusqu'à 110 pods par nœud. Vous ne pouvez pas configurer les pods maximum par nœud une fois que vous avez déjà déployé un cluster AKS, ou si vous déployez un cluster à l’aide du portail Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
