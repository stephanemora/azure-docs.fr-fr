---
title: Réparation automatique des nœuds Azure Kubernetes Service
description: Découvrez la fonctionnalité de réparation automatique des nœuds et la façon dont AKS corrige les nœuds Worker endommagés.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284838"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Réparation automatique des nœuds AKS

AKS vérifie en permanence l’état d’intégrité des nœuds Worker et effectue une réparation automatique des nœuds s’ils ne sont pas sains. Cette documentation décrit la façon dont Azure Kubernetes Service (AKS) supervise les nœuds Worker et répare les nœuds Worker non sains.  La documentation vise à informer les opérateurs AKS sur le comportement de la fonctionnalité de réparation des nœuds. Il est également important de noter que la plateforme Azure [effectue des opérations de maintenance sur Machines Virtuelles][vm-updates] qui rencontrent des problèmes. AKS et Azure fonctionnent ensemble, de façon à réduire les interruptions de service pour vos clusters.

> [!Important]
> La fonctionnalité de réparation automatique des nœuds n’est actuellement pas prise en charge pour les pools de nœuds Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Comment AKS vérifie les nœuds non sains

> [!Note]
> AKS effectue une action de réparation sur les nœuds avec le compte d’utilisateur **aks-remediator**.

AKS utilise des règles pour déterminer si un nœud est dans un état non sain et s’il doit être réparé. AKS utilise les règles suivantes pour déterminer si une réparation automatique est nécessaire.

* Le nœud signale l’état de **NotReady** lors de vérifications consécutives sur une période de 10 minutes
* Le nœud ne signale pas d’état pendant 10 minutes

Vous pouvez vérifier manuellement l’état d’intégrité de vos nœuds avec kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Fonctionnement de la réparation automatique

> [!Note]
> AKS effectue une action de réparation sur les nœuds avec le compte d’utilisateur **aks-remediator**.

Ce comportement est destiné à **Virtual Machine Scale Sets**.  La réparation automatique s’effectue en plusieurs étapes pour réparer un nœud endommagé.  Si un nœud est identifié comme non sain, AKS tente plusieurs étapes de correction.  Les étapes sont effectuées dans cet ordre :

1. À partir du moment où le runtime du conteneur ne répond plus pendant 10 minutes, les services d’exécution en échec sont redémarrés sur le nœud.
2. Si le nœud n’est pas prêt dans un délai de 10 minutes, il est redémarré.
3. Si le nœud n’est pas prêt dans les 30 minutes, il est recréé.

> [!Note]
> Si plusieurs nœuds ne sont pas sains, ils sont réparés un par un.

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Zones de disponibilité][availability-zones] pour augmenter la haute disponibilité avec les charges de travail de votre cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
