---
title: Mettre à l’échelle un cluster Azure Kubernetes Service (AKS)
description: Apprenez à mettre le nombre de nœuds à l'échelle dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 9cc06df5d2a66ede18af52c13201c731c12e2049
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614508"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Mettre le nombre de nœuds à l'échelle dans un cluster Azure Kubernetes Service (AKS)

Si les besoins en ressources de vos applications évoluent, vous pouvez procéder manuellement à la mise à l'échelle d'un cluster AKS pour exécuter un nombre de nœuds différent. Si vous réduisez l’échelle, les nœuds sont soigneusement [coordonnés et purgés][kubernetes-drain] afin de limiter les perturbations pour les applications en cours d’exécution. Si vous effectuez une montée en puissance, AKS attend que les nœuds soient marqués `Ready` par le cluster Kubernetes avant que des pods soient planifiés dans ces nœuds.

## <a name="scale-the-cluster-nodes"></a>Mettre à l’échelle les nœuds de cluster

Commencez par vous procurer le *nom* de votre pool de nœuds à l’aide de la commande [az aks show][az-aks-show]. Dans l'exemple suivant, cette commande permet d’obtenir le nom du pool de nœuds du cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

L'exemple suivant montre que le *nom* est *nodepool1* :

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Utilisez la commande [az aks scale][az-aks-scale] pour mettre à l’échelle les nœuds du cluster. L’exemple suivant met à l’échelle un cluster nommé *myAKSCluster* vers un nœud unique. Indiquez le nom du pool de nœuds ( *--nodepool-name*) obtenu à l'aide de la commande précédente, par exemple *nodepool1* :

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

L'exemple suivant montre que le cluster a été mis à l'échelle avec succès sur un nœud, comme indiqué dans la section *agentPoolProfiles* :

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez mis à l’échelle un cluster AKS manuellement pour augmenter ou réduire le nombre de nœuds. Vous pouvez également utiliser l’[autoscaler de cluster][cluster-autoscaler] (actuellement en préversion dans AKS) pour mettre automatiquement à l’échelle votre cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
