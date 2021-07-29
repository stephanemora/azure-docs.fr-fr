---
title: Réparation automatique des nœuds Azure Kubernetes Service
description: Découvrez la fonctionnalité de réparation automatique des nœuds et la façon dont AKS corrige les nœuds Worker endommagés.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 95783000739fd42e39ef1bbce88c3b8e62d4a8dc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073195"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Réparation automatique des nœuds AKS

AKS surveille en permanence l’état d’intégrité des nœuds Worker et effectue une réparation automatique des nœuds s’ils ne sont pas sains. La plateforme de machines virtuelles Azure [effectue la maintenance des machines virtuelles][vm-updates] qui rencontrent des problèmes. 

AKS et les machines virtuelles Azure opèrent ensemble pour réduire les interruptions de service pour vos clusters.

Dans ce document, vous allez découvrir le comportement de la fonctionnalité de réparation automatique des nœuds pour les nœuds Windows et Linux. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Comment AKS vérifie les nœuds non sains

AKS utilise les règles suivantes pour déterminer si un nœud est dans un état non sain et nécessite une réparation : 
* Le nœud signale l’état **NotReady** lors de vérifications consécutives sur une période de 10 minutes.
* Le nœud ne signale aucun état pendant 10 minutes.

Vous pouvez vérifier manuellement l’état d’intégrité de vos nœuds avec kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Fonctionnement de la réparation automatique

> [!Note]
> AKS lance des opérations de réparation avec le compte d’utilisateur **aks-remediator**.

Si AKS identifie un nœud non sain qui reste dans cet état pendant 10 minutes, AKS effectue les actions suivantes :

1. Redémarrez le nœud.
1. Si le redémarrage échoue, réinitialisez le nœud.
1. Si la réinitialisation échoue, créez et réinitialisez un nouveau nœud.

Des solutions alternatives sont étudiées par les ingénieurs d’AKS si la réparation automatique échoue. 

Si AKS trouve plusieurs nœuds non sains pendant un contrôle d’intégrité, chaque nœud est réparé individuellement avant qu’une autre réparation ne commence.


## <a name="limitations"></a>Limites

Dans de nombreux cas, AKS peut déterminer si un nœud est défectueux et tenter de résoudre le problème, mais dans certains cas, AKS ne peut pas réparer le problème ou détecter qu’il y a un problème. Par exemple, AKS ne peut pas détecter les problèmes si l’état d’un nœud n’est pas signalé en raison d’une erreur dans la configuration du réseau.

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Zones de disponibilité][availability-zones] pour augmenter la haute disponibilité avec les charges de travail de votre cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
