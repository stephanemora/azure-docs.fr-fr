---
title: Réparation automatique des nœuds Azure Kubernetes Service
description: Découvrez la fonctionnalité de réparation automatique des nœuds et la façon dont AKS corrige les nœuds Worker endommagés.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 7fcb7b380f3694aaf34328019c3e09f5157c9e64
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542040"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Réparation automatique des nœuds AKS

AKS vérifie en permanence l’état d’intégrité des nœuds Worker et effectue une réparation automatique des nœuds s’ils ne sont pas sains. Ce document informe les opérateurs sur le comportement de la fonctionnalité de réparation automatique de nœud. En plus des réparations AKS, la plateforme de machine virtuelle Azure [effectue une maintenance sur les machines virtuelles][vm-updates] qui rencontrent des problèmes. AKS et les machines virtuelles Azure opèrent ensemble pour réduire les interruptions de service pour vos clusters.

## <a name="limitations"></a>Limites

* Les pools de nœuds Windows ne sont pas pris en charge actuellement.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Comment AKS vérifie les nœuds non sains

AKS utilise des règles pour déterminer si un nœud est dans un état non sain et nécessite une réparation. AKS utilise les règles suivantes pour déterminer si une réparation automatique est nécessaire.

* Le nœud signale l’état de **NotReady** lors de vérifications consécutives sur une période de 10 minutes
* Le nœud ne signale pas d’état pendant 10 minutes

Vous pouvez vérifier manuellement l’état d’intégrité de vos nœuds avec kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Fonctionnement de la réparation automatique

> [!Note]
> AKS lance des opérations de réparation avec le compte d’utilisateur **aks-remediator**.

Si un nœud est considéré comme non sain en vertu des règles ci-dessus et reste non sain pendant 10 minutes consécutives, AKS redémarre le nœud. Si des nœuds restent en état non sain après l’opération de réparation initiale, les ingénieurs AKS étudient des corrections supplémentaires.
  
Si plusieurs nœuds sont en état non sain pendant un contrôle d’intégrité, chaque nœud est réparé avant le début de la réparation d’un autre.

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Zones de disponibilité][availability-zones] pour augmenter la haute disponibilité avec les charges de travail de votre cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
