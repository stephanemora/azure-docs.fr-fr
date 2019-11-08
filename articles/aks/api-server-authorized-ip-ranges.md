---
title: Plages d’adresses IP autorisées par le serveur d’API dans Azure Kubernetes Service (AKS)
description: Apprenez à sécuriser votre cluster à l’aide d’une plage d’adresses IP pour l’accès au serveur d’API dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472969"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Sécuriser l’accès au serveur d’API à l’aide de plages d’adresses IP autorisées dans Azure Kubernetes Service (AKS)

Dans Kubernetes, le serveur d’API reçoit des requêtes pour effectuer des actions dans le cluster, comme la création de ressources ou la mise à l’échelle du nombre de nœuds. Le serveur d’API est le moyen centralisé pour interagir avec et gérer un cluster. Pour améliorer la sécurité du cluster et minimiser les attaques, le serveur d’API doit uniquement être accessible à partir d’un ensemble limité de plages d’adresses IP.

Cet article vous montre comment utiliser des plages d’adresses IP autorisées pour le serveur d’API pour définir quelles adresses IP et CIDR peuvent accéder plan de contrôle.

> [!IMPORTANT]
> Sur les nouveaux clusters, les plages d’adresses IP autorisées du serveur d’API sont uniquement prises en charge sur l’équilibreur de charge SKUS *Standard*. Les clusters existants avec l’équilibreur de charge SKU *De base* et les plages d’adresses IP autorisées du serveur d’API sont configurés pour continuer à fonctionner normalement. Ces clusters continueront de fonctionner pendant leur mise à niveau.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous travaillez avec des clusters qui utilisent [kubenet][kubenet].  Avec les clusters basés sur [Azure Container Networking Interface (CNI)][cni-networking], vous ne disposez pas de la table de routage nécessaire pour sécuriser l’accès.  Vous devrez créer manuellement la table de routage.  Pour plus d’informations, consultez [Gestion des tables de routage](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS que vous créez. Cet article vous montre comment créer un cluster AKS à l’aide d’Azure CLI.

La version 2.0.76 d’Azure CLI (ou ultérieure) doit être installée et configurée. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous configurez des plages d’adresses IP autorisées pour le serveur d’API :

* Vous ne pouvez actuellement pas utiliser Azure Dev Spaces, car la communication avec le serveur d’API est également bloquée.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Vue d’ensemble des plages d’adresses IP pour le serveur d’API

Le serveur d’API Kubernetes détermine la façon dont les API Kubernetes sous-jacentes sont exposées. Ce composant fournit l’interaction des outils de gestion, tels que `kubectl` ou le tableau de bord Kubernetes. AKS fournit un maître de cluster monolocataire doté d’un serveur d’API dédié. Par défaut, le serveur d’API reçoit une adresse IP publique, et vous devez contrôler l’accès à l’aide de contrôles d’accès en fonction du rôle (RBAC).

Pour sécuriser l’accès au plan de contrôle AKS/serveur d’API, normalement accessible, vous pouvez activer et utiliser des plages d’adresses IP autorisées. Ces plages d’adresses IP autorisées autorisent uniquement les plages d’adresses IP définies à communiquer avec le serveur d’API. Une demande adressée au serveur d’API depuis une adresse IP qui ne fait pas partie de ces plages d’adresses IP autorisées est bloquée. Vous devez continuer à utiliser RBAC pour autoriser des utilisateurs et les actions qu’ils demandent.

Pour plus d’informations sur le serveur d’API et les autres composants de cluster, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS. Vous ne pouvez pas activer les plages d’adresses IP autorisées dans le cadre de l’opération de création du cluster. Si vous essayez d’activer les plages IP autorisées dans le cadre du processus de création du cluster, les nœuds de cluster ne peuvent pas à accéder au serveur d’API pendant le déploiement, car l’adresse IP de sortie n’est pas définie à ce stade.

D’abord, créez un cluster avec la commande [az aks create][az-aks-create]. L’exemple suivant crée un cluster à nœud unique nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Mettre à jour un cluster avec des plages d’adresses IP autorisées

Par défaut, votre cluster utilise l’[équilibreur de charge SKU Standard][standard-sku-lb], que vous pouvez utiliser pour configurer la passerelle sortante. Utilisez [az network public-ip list][az-network-public-ip-list] et spécifiez le groupe de ressources de votre cluster AKS, qui commence généralement par *MC_* . Cela a pour effet d’afficher l’adresse IP publique de votre cluster, que vous pouvez autoriser. Utilisez la commande [az aks update][az-aks-update] et spécifiez le paramètre *--api-server-authorized-ip-ranges* pour autoriser l’IP du cluster. Par exemple :

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Pour activer les plages d’adresses IP autorisées du serveur d’API, utilisez la commande [az aks update][az-aks-update] et spécifiez le paramètre *--api-server-authorized-ip-ranges* pour fournir une liste de plages d’adresses IP autorisées. Ces plages d’adresses IP sont généralement des plages d’adresses utilisées par vos réseaux locaux ou IP publiques. Lorsque vous spécifiez une plage CIDR, commencez par la première adresse IP dans la plage. Par exemple, *137.117.106.90/29* est une plage valide, mais assurez-vous que vous spécifiez la première adresse IP dans la plage, par exemple *137.117.106.88/29*.

L’exemple suivant active les plages d’adresses IP autorisées pour le serveur d’API sur le cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Les plages d’adresses IP à autoriser sont *172.0.0.0/16* (plage d’adresses des pods/nœuds) et *168.10.0.0/18* (ServiceCidr) :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Vous devez ajouter ces plages à une liste verte :
> - Adresse IP publique du pare-feu
> - CIDR du service
> - Plage d’adresses des sous-réseaux, avec les nœuds et les pods
> - Toute plage qui représente les réseaux à partir desquels vous allez administrer le cluster

## <a name="disable-authorized-ip-ranges"></a>Désactiver les plages d’adresses IP autorisées

Pour désactiver les plages d’adresses IP autorisées, utilisez [az aks update][az-aks-update] et spécifiez une plage vide pour désactiver les plages d’adresses IP autorisées du serveur d’API. Par exemple :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez activé des plages d’adresses IP autorisées pour le serveur d’API. Cette approche est une partie de la façon dont vous pouvez exécuter un cluster AKS sécurisé.

Pour plus d’informations, consultez [Concepts de sécurité pour les applications et les clusters dans AKS][concepts-security] et [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
