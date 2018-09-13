---
title: Fichier Include
description: Fichier Include
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667051"
---
| Ressource | Limite par défaut |
| --- | :--- |
| Clusters maximum par abonnement | 100 |
| Nœuds maximum par cluster | 100 |
| Pods maximum par nœud : [réseau de base][basic-networking] avec Kubenet | 110 |
| Pods maximum par nœud : [mise en réseau avancée][advanced-networking] avec Azure CNI | Déploiement de l’interface de ligne de commande Azure : 110<sup>1</sup><br />Modèle Resource Manager : 110<sup>1</sup><br />Déploiement du portail : 30 |

<sup>1</sup> Cette valeur est configurable au moment du déploiement d’un cluster AKS avec l’interface de ligne de commande Azure ou un modèle Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
