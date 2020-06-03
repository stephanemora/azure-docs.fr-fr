---
title: Azure Kubernetes Service (AKS) avec contrat SLA de durée de fonctionnement
description: Découvrez l’offre de Contrat SLA de durée de fonctionnement facultative pour le serveur d’API Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 2df0ad675f03b25363ab0f5b13dceb762a657ed7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299551"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Contrat SLA de durée de fonctionnement Azure Kubernetes Service (AKS)

Le contrat SLA de durée de fonctionnement est une fonctionnalité facultative permettant de bénéficier d’un contrat SLA soutenu et élevé pour un cluster. Le contrat SLA de durée de fonctionnement garantit une disponibilité de 99,95 % du point de terminaison de serveur de l’API Kubernetes pour les clusters qui utilisent des [Zones de disponibilité][availability-zones] et 99,9 % de disponibilité pour les clusters qui n’utilisent pas de Zones de disponibilité. AKS utilise les réplicas de nœud principal entre les domaines de mise à jour et d’erreur pour garantir la satisfaction des exigences de contrat SLA.

Les clients ayant besoin d’un contrat SLA pour répondre aux exigences de conformité ou nécessitant l’extension d’un contrat SLA à leurs utilisateurs finaux doivent activer cette fonctionnalité. Les clients avec des charges de travail critiques qui bénéficieront d’un contrat SLA de durée de fonctionnement plus élevée peuvent également en bénéficier. L’utilisation de la fonctionnalité Contrat SLA de durée de fonctionnement avec les Zones de disponibilité permet d’obtenir une disponibilité plus élevée pour la durée de bon fonctionnement du serveur d’API Kubernetes.  

Les clients peuvent toujours créer un nombre illimité de clusters gratuits avec un objectif de niveau de service (SLO) de 99,5 % et opter pour leur durée de bon fonctionnement SLO ou SLA préférée en fonction des besoins.

> [!Important]
> Pour les clusters avec verrouillage de sortie, consultez [limiter le trafic de sortie](limit-egress-traffic.md) pour ouvrir les ports appropriés.

## <a name="sla-terms-and-conditions"></a>Conditions générales des contrats SLA

Le contrat SLA de durée de fonctionnement est une fonctionnalité payante et activée par cluster. Le tarif du contrat SLA de durée de fonctionnement est déterminé par le nombre de clusters discrets, et non par la taille des clusters individuels. Consultez [Détails de la tarification du contrat SLA de durée de fonctionnement](https://azure.microsoft.com/pricing/details/kubernetes-service/) pour en savoir plus.

## <a name="before-you-begin"></a>Avant de commencer

* Azure CLI version 2.7.0 ou ultérieure

## <a name="creating-a-cluster-with-uptime-sla"></a>Création d’un cluster avec un contrat SLA de durée de fonctionnement

Pour créer un nouveau cluster avec le contrat SLA de durée de fonctionnement, vous utilisez Azure CLI.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS. L’exemple suivant crée un cluster à un nœud nommé *myAKSCluster*. Il est également possible d’activer Azure Monitor pour conteneurs à l’aide du paramètre *--enable-addons monitoring*.  Cette opération prend plusieurs minutes.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster. L’extrait de code JSON suivant montre le niveau payant pour la référence (SKU), indiquant que votre cluster est activé avec un contrat SLA de durée de fonctionnement.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Limites

* Actuellement, la conversion en cluster existant pour activer le contrat SLA de durée de fonctionnement n’est pas possible.
* Actuellement, il n’existe aucun moyen de supprimer le contrat SLA de durée de fonctionnement d’un cluster AKS après la création lorsqu’il est activé.  
* Les clusters privés ne sont actuellement pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Zones de disponibilité][availability-zones] pour augmenter la haute disponibilité avec les charges de travail de votre cluster AKS.
Configurez votre cluster pour [limiter le trafic de sortie](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
