---
title: Limites pour les ressources, les références SKU et les régions
titleSuffix: Azure Kubernetes Service
description: Découvrez les quotas par défaut, les tailles des références SKU des machines virtuelles de nœud restreint ainsi que la disponibilité des régions d’Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 6e965bd958b23598f7b11dcf9a12dafb33f26680
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437889"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas, restrictions de taille de machine virtuelle et disponibilité des régions dans Azure Kubernetes Service (AKS)

Tous les services Azure définissent des limites et des quotas par défaut pour les ressources et les fonctionnalités, notamment des restrictions d’utilisation pour certaines références SKU de machines virtuelles.

Cet article détaille les limites par défaut des ressources Azure Kubernetes Service (AKS) et la disponibilité d’AKS dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastructure approvisionnée

Toutes les autres limitations réseau, de calcul et de stockage s’appliquent à l’infrastructure approvisionnée. Pour connaître les limites appropriées, consultez [Limites du service et de l’abonnement Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Lorsque vous mettez à niveau un cluster AKS, des ressources supplémentaires sont consommées momentanément. Ces ressources incluent les adresses IP disponibles dans un sous-réseau de réseau virtuel ou le quota de processeurs virtuels de machines virtuelles. 
>
> Pour les conteneurs Windows Server, vous pouvez effectuer une opération de mise à niveau pour appliquer les dernières mises à jour des nœuds. Si vous ne disposez pas de l’espace d’adressage IP ou du quota de processeurs virtuels disponibles pour gérer ces ressources temporaires, le processus de mise à niveau du cluster échoue. Pour plus d’informations sur la mise à niveau d’un nœud Windows Server, voir [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

## <a name="supported-vm-sizes"></a>Tailles des machines virtuelles prises en charge

La liste des tailles de machines virtuelles prises en charge dans AKS évolue avec la publication de nouveaux niveaux tarifaires de machine virtuelle dans Azure. Veuillez suivre les [notes de publication d’AKS](https://github.com/Azure/AKS/releases) pour rester informé des nouveaux niveaux tarifaires pris en charge.

## <a name="restricted-vm-sizes"></a>Tailles de machines virtuelles limitées

Chaque nœud d’un cluster AKS contient une quantité fixe de ressources de calcul telles que des processeurs virtuels et de la mémoire. Si un nœud AKS contient des ressources de calcul insuffisantes, l’exécution des pods peut être incorrecte. Pour vous assurer de pouvoir planifier les pods *kube-system*, ainsi que vos applications de façon fiable, **n’utilisez pas les références SKU de machine virtuelle suivantes dans AKS** :

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1ls
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s
- Standard_A2
- D1 standard
- Standard_D1_v2
- Standard_DS1
- Standard_DS1_v2

Pour plus d’informations sur les types de machines virtuelles et leurs ressources de calcul, consultez [Tailles des machines virtuelles dans Azure][vm-skus].

## <a name="region-availability"></a>Disponibilité des régions

Pour obtenir la liste la plus récente des emplacements où vous pouvez déployer et exécuter des clusters, consultez [Disponibilité des régions AKS][region-availability].

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Préréglages de configuration de cluster dans le portail Azure

Lorsque vous créez un cluster à l’aide du portail Azure, vous pouvez choisir une configuration prédéfinie pour personnaliser rapidement en fonction de votre scénario. Vous pouvez modifier les valeurs prédéfinies à tout moment.

| Préréglage           | Description                                                            |
|------------------|------------------------------------------------------------------------|
| standard         | Idéal si vous n’êtes pas sûr de ce que vous devez choisir. Fonctionne bien avec la plupart des applications. |
| Développement/Test         | Idéal pour expérimenter avec AKS ou déployer une application de test. |
| Optimisation des coûts   | Idéal pour réduire les coûts sur les charges de travail de production qui peuvent tolérer des interruptions. |
| Traitement par lots | Idéal pour les charges de travail de Machine Learning, de calcul intensif et nécessitant beaucoup de ressources graphiques. Adapté aux applications nécessitant un scale-up et un scale-out rapides du cluster. |
| Accès renforcé  | Idéal pour les grandes entreprises qui ont besoin d’un contrôle total de la sécurité et de la stabilité. |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez augmenter des limites par défaut et des quotas. Si votre ressource prend en charge les augmentations, demandez-en une via une [demande de support Azure][azure-support] (pour **Type de problème**, sélectionnez **Quota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
