---
title: Quotas, références (SKU) et disponibilité des régions dans Azure Kubernetes Service (ACS)
description: Découvrez les quotas par défaut, tailles de référence (SKU) de machine virtuelle de nœud limité et disponibilité des régions Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901460"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas, les restrictions de taille de machine virtuelle et disponibilité des régions dans Azure Kubernetes Service (ACS)

Tous les services Azure définir des quotas pour les ressources et les fonctionnalités et les limites par défaut. Certaines références (SKU) de machine virtuelle (VM) est également limitées à utiliser.

Cet article décrit en détail les limites de ressources par défaut pour les ressources Azure Kubernetes Service (AKS) et la disponibilité de AKS dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastructure approvisionnée

Toutes les autres limitations réseau, de calcul et de stockage s’appliquent à l’infrastructure approvisionnée. Pour les limites appropriées, consultez [abonnement Azure et limites de service](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Lorsque vous mettez à niveau un cluster AKS, des ressources supplémentaires sont consommées momentanément. Ces ressources incluent les adresses IP disponibles dans un sous-réseau de réseau virtuel ou le quota de processeurs virtuels de machine virtuelle. Si vous utilisez des conteneurs Windows Server (actuellement en version préliminaire dans ACS), la seule approuvées pour appliquer les dernières mises à jour aux nœuds consiste à effectuer une opération de mise à niveau. Un processus de mise à niveau de cluster ayant échoué peut indiquer que vous n’avez le IP adresse espace ou processeurs virtuels quota disponible pour gérer ces ressources temporaires. Pour plus d’informations sur le processus de mise à niveau du nœud Windows Server, consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tailles des machines virtuelles restreintes

Chaque nœud dans un cluster AKS contient une quantité fixe de ressources de calcul tels que les processeurs virtuels et de la mémoire. Si un nœud AKS contient des ressources de calcul insuffisante, pods peut ne pas s’exécuter correctement. Pour vous assurer que le texte requis *kube-system* pods et vos applications fiable peut être planifié, n’utilisez pas les références SKU de machine virtuelle suivantes dans ACS :

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Pour plus d’informations sur les types de machines virtuelles et leurs ressources de calcul, consultez [tailles des machines virtuelles dans Azure][vm-skus].

## <a name="region-availability"></a>Disponibilité dans la région

Pour obtenir la liste de l’emplacement où vous pouvez déployer et exécuter des clusters, consultez [disponibilité des régions AKS][region-availability].

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Si votre ressource prend en charge une augmentation, demander l’augmentation via un [demande de support Azure] [ azure-support] (pour **type de problème**, sélectionnez **Quota** ).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
