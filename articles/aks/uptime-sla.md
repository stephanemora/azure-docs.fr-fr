---
title: Azure Kubernetes Service (AKS) avec contrat SLA de durée de fonctionnement
description: Découvrez l’offre de Contrat SLA de durée de fonctionnement facultative pour le serveur d’API Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 77bdfb4ed75b9287b911a6b0d2742c235b37e297
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563929"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Contrat SLA de durée de fonctionnement Azure Kubernetes Service (AKS)

Le contrat de niveau de service de durée de fonctionnement est un moyen permettant d’activer un contrat de niveau de service soutenu financièrement et plus élevé pour un cluster AKS. Les clusters avec un contrat de niveau de service de durée de bon fonctionnement, également considéré comme un niveau payant dans les API REST AKS, sont livrés avec une plus grande quantité de ressources de plan de contrôle et sont automatiquement mis à l’échelle pour répondre à la charge de votre cluster. Le contrat SLA de durée de fonctionnement garantit une disponibilité de 99,95 % du point de terminaison de serveur de l’API Kubernetes pour les clusters qui utilisent des [Zones de disponibilité][availability-zones] et 99,9 % de disponibilité pour les clusters qui n’utilisent pas de Zones de disponibilité. AKS utilise les réplicas de nœud principal entre les domaines de mise à jour et d’erreur pour garantir la satisfaction des exigences de contrat SLA.

AKS recommande l’utilisation d’un contrat de niveau de service de durée de bon fonctionnement dans les charges de travail de production pour garantir la disponibilité des composants de plan de contrôle. En revanche, les clusters de niveau gratuit sont fournis avec moins de réplicas et des ressources limitées pour le plan de contrôle et ne conviennent pas aux charges de travail de production.

Les clients peuvent toujours créer un nombre illimité de clusters gratuits avec un objectif de niveau de service (SLO) de 99,5 % et opter pour leur SLO préféré. 

> [!IMPORTANT]
> Pour les clusters avec verrouillage de sortie, consultez [limiter le trafic de sortie](limit-egress-traffic.md) pour ouvrir les ports appropriés.

## <a name="region-availability"></a>Disponibilité des régions

* Le contrat SLA de durée de fonctionnement est disponible dans les régions publiques et dans les régions Azure Government où [AKS est pris en charge](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* Le contrat SLA de durée de fonctionnement est disponible pour les [clusters AKS privés][private-clusters] dans toutes les régions publiques où AKS est pris en charge.

## <a name="sla-terms-and-conditions"></a>Conditions générales des contrats SLA

Le contrat de niveau de service de durée de bon fonctionnement est une fonctionnalité payante activée par cluster. Le tarif du contrat SLA de durée de fonctionnement est déterminé par le nombre de clusters discrets, et non par la taille des clusters individuels. Consultez [Détails de la tarification du contrat SLA de durée de fonctionnement](https://azure.microsoft.com/pricing/details/kubernetes-service/) pour en savoir plus.

## <a name="before-you-begin"></a>Avant de commencer

* Installez [Azure CLI](/cli/azure/install-azure-cli) version 2.8.0 ou ultérieure

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Création d’un cluster avec un contrat SLA de durée de fonctionnement

Pour créer un nouveau cluster avec le contrat SLA de durée de fonctionnement, vous utilisez Azure CLI.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Utilisez la commande [`az aks create`][az-aks-create] pour créer un cluster AKS. L’exemple suivant crée un cluster à un nœud nommé *myAKSCluster*. Cette opération prend plusieurs minutes :

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```

Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster. L’extrait de code JSON suivant montre le niveau payant pour la référence SKU, indiquant que votre cluster est activé avec un contrat SLA de durée de fonctionnement :

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modifier un cluster existant pour utiliser un contrat SLA de durée de fonctionnement

Vous pouvez éventuellement mettre à jour vos clusters existants pour utiliser un contrat SLA de durée de fonctionnement.

Si vous aviez créé un cluster AKS lors des étapes précédentes, supprimez le groupe de ressources :

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Créez un autre groupe de ressources :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Créez un autre cluster, sans utiliser de contrat SLA de durée de fonctionnement :

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Utilisez la commande [`az aks update`][az-aks-update] pour mettre à jour le cluster existant :

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

L’extrait de code JSON suivant montre le niveau payant pour la référence SKU, indiquant que votre cluster est activé avec un contrat SLA de durée de fonctionnement :

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="opt-out-of-uptime-sla"></a>Refuser le contrat de niveau de service de durée de bon fonctionnement

Vous pouvez mettre à jour votre cluster pour passer au niveau gratuit et refuser le contrat de niveau de service de durée de bon fonctionnement.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
```

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais, nettoyez toutes les ressources que vous avez créées. Pour supprimer le cluster, utilisez la commande [`az group delete`][az-group-delete] qui supprime le groupe de ressources AKS :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Zones de disponibilité][availability-zones] pour augmenter la haute disponibilité avec les charges de travail de votre cluster AKS.

Configurez votre cluster pour [limiter le trafic de sortie](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
