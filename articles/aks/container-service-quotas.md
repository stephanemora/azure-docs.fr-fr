---
title: Disponibilité des régions et quotas pour Azure Kubernetes Service (AKS)
description: La disponibilité des régions et les quotas par défaut d’Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: iainfou
ms.openlocfilehash: 8feeaf2e8ee99405ed0de8291fc97dc50db6a386
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805225"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Disponibilité des régions et quotas pour Azure Kubernetes Service (AKS)

Tous les services Azure incluent certains quotas et limites par défaut pour les ressources et fonctionnalités. Les sections suivantes détaillent les limites de ressources par défaut pour plusieurs ressources Azure Kubernetes Service (AKS), ainsi que la disponibilité du service AKS dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastructure approvisionnée

Toutes les autres limitations réseau, de calcul et de stockage s’appliquent à l’infrastructure approvisionnée. Consultez [Limites du service et de l’abonnement Azure](../azure-subscription-service-limits.md) pour les limites appropriées.

## <a name="region-availability"></a>Disponibilité des régions

Azure Kubernetes Service (AKS) est disponible dans les régions suivantes :

- Australie Est
- Australie Sud-Est
- Centre du Canada
- Est du Canada
- Inde Centre
- USA Centre
- Asie Est
- USA Est
- Est des États-Unis 2
- France Centre
- Japon Est
- Centre de la Corée
- Corée du Sud
- Europe Nord
- Asie Sud-Est
- USA Centre Sud
- Inde Sud
- Sud du Royaume-Uni
- Ouest du Royaume-Uni
- Europe Ouest
- USA Ouest
- USA Ouest 2

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Pour demander une augmentation d’une ou de plusieurs ressources qui peuvent prendre en charge cette augmentation, envoyez une [demande de support Azure][azure-support] (sélectionnez « Quota » pour **Type de problème**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
