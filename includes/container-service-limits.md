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
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072880"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nombre maximal de nœuds par cluster | 100 |
| Pods maximum par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Pods maximum par nœud : [Avancées de mise en réseau] [ advanced-networking] avec l’Interface de mise en réseau de conteneur Azure | Déploiement de l’interface Azure CLI : 30<sup>1</sup><br />Modèle de gestionnaire de ressources Azure : 30<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup>lorsque vous déployez un cluster Azure Kubernetes Service (AKS) avec l’interface CLI ou un modèle Resource Manager, cette valeur est configurable jusqu'à 250 pods par nœud. Vous ne pouvez pas configurer les pods maximum par nœud une fois que vous avez déjà déployé un cluster AKS, ou si vous déployez un cluster à l’aide du portail Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
