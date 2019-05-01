---
title: Quotas, référence (SKU) et disponibilité des régions dans Azure Kubernetes Service (ACS)
description: Découvrez les quotas par défaut, tailles de référence (SKU) de machine virtuelle de nœud limité et disponibilité des régions Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413022"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas, les restrictions de taille de machine virtuelle et disponibilité des régions dans Azure Kubernetes Service (ACS)

Tous les services Azure incluent certains quotas et limites par défaut pour les ressources et fonctionnalités. Pour la taille du nœud, certaines machines virtuelles (VM) références (SKU) est ensuite utilisation limitée.

Cet article décrit en détail les limites de ressources par défaut pour les ressources Azure Kubernetes Service (AKS), ainsi que la disponibilité du service AKS dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastructure approvisionnée

Toutes les autres limitations réseau, de calcul et de stockage s’appliquent à l’infrastructure approvisionnée. Consultez [Limites du service et de l’abonnement Azure](../azure-subscription-service-limits.md) pour les limites appropriées.

## <a name="restricted-vm-sizes"></a>Tailles des machines virtuelles restreintes

Chaque nœud dans un cluster AKS contient une quantité fixe de ressources de calcul tels que les processeurs virtuels et de la mémoire. Si un nœud AKS contient des ressources de calcul insuffisante, pods peuvent ne pas s’exécuter correctement. Pour vous assurer que le texte requis *kube-system* pods et vos applications peuvent être planifiées fiable, les références SKU de machine virtuelle suivantes ne peut pas être utilisées dans ACS :

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Pour plus d’informations sur les types de machines virtuelles et leurs ressources de calcul, consultez [tailles des machines virtuelles dans Azure][vm-skus].

## <a name="region-availability"></a>Disponibilité des régions

Pour obtenir la liste de l’emplacement où vous pouvez déployer et exécuter des clusters, consultez [disponibilité des régions AKS][region-availability].

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Pour demander une augmentation d’une ou de plusieurs ressources qui peuvent prendre en charge cette augmentation, envoyez une [demande de support Azure][azure-support] (sélectionnez « Quota » pour **Type de problème**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
