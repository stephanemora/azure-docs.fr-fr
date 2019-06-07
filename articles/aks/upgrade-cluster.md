---
title: Mise à jour d’un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment mettre à niveau un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692780"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Mise à jour d’un cluster Azure Kubernetes Service (AKS)

Dans le cadre du cycle de vie d’un cluster AKS, vous devez souvent opérer une mise à niveau vers la dernière version de Kubernetes. Il est important d’appliquer les dernières publications de sécurité de Kubernetes, ou d’opérer une mise à niveau pour obtenir les dernières fonctionnalités. Cet article vous montre comment mettre à niveau les composants maîtres ou un seul pool de nœud par défaut dans un cluster AKS.

Pour AKS clusters qui utilisent plusieurs pools de nœuds ou des nœuds de serveur Windows (les deux actuellement en version préliminaire dans ACS), consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].

## <a name="before-you-begin"></a>Avant de commencer

Cet article nécessite que vous exécutiez Azure CLI version 2.0.65 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Recherchez les mises à niveau du cluster AKS disponibles

Pour rechercher les publications de Kubernetes disponibles pour votre cluster, utilisez la commande [az aks get-upgrades][az-aks-get-upgrades]. L’exemple suivant recherche les mises à niveau disponibles pour le cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Lors de la mise à niveau d’un cluster AKS, les versions mineures de Kubernetes ne peuvent pas être ignorées. Par exemple, la mise à niveau entre *1.11.x* -> *1.12.x* ou *1.12.x* -> *1.13.x* sont cependant autorisées *1.11.x* -> *1.13.x* n’est pas.
>
> Pour mettre à niveau, à partir de *1.11.x* -> *1.13.x*, la première mise à niveau à partir de *1.11.x* -> *1.12.x*, puis mettez à niveau à partir de *1.12.x* -> *1.13.x*.

L’exemple de sortie suivant montre que le cluster peut être mis à niveau vers la version *1.12.7* ou *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Mettre à niveau un cluster AKS

Avec une liste des versions disponibles pour votre cluster AKS, utilisez la commande [az aks upgrade] [ az-aks-upgrade] pour opérer la mise à niveau. Pendant le processus de mise à niveau, AKS ajoute un nouveau nœud au cluster qui exécute la version de Kubernetes spécifiée, puis soigneusement [DRAIN et cordon diminue] [ kubernetes-drain] un des nœuds ancien pour limiter les perturbations pour l’exécution applications. Lorsque le nouveau nœud est confirmé comme pods d’application en cours d’exécution, l’ancien nœud est supprimé. Ce processus se répète jusqu'à ce que tous les nœuds du cluster ont été mis à niveau.

L’exemple suivant met à niveau un cluster à la version *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Quelques minutes suffisent pour mettre à niveau le cluster. Le temps nécessaire varie en fonction du nombre de nœuds.

Pour vérifier si la mise à niveau a réussi, utilisez la commande [az aks show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie suivant montre que le cluster s’exécute désormais *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment mettre à niveau un cluster AKS existant. Pour en savoir plus sur le déploiement et la gestion des clusters d’AKS, reportez-vous aux didacticiels.

> [!div class="nextstepaction"]
> [Didacticiels AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
