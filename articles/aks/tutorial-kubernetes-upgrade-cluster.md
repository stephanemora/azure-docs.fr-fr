---
title: Tutoriel Kubernetes sur Azure - Mettre à jour un cluster
description: Tutoriel Kubernetes sur Azure - Mettre à jour un cluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131641"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutoriel : mettre à niveau Kubernetes dans Azure Kubernetes Service (AKS)

Un cluster Azure Kubernetes Service (AKS) peut être mis à niveau à l’aide d’Azure CLI. Pour limiter les perturbations pour les applications en cours d’exécution, les nœuds Kubernetes sont soigneusement [coordonnés et vidés][kubernetes-drain] pendant le processus de mise à niveau.

Dans ce tutoriel (le septième d’une série de sept), un cluster Kubernetes est mis à niveau. Les tâches que vous effectuez sont les suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Tutoriel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Obtenir les versions du cluster

Avant la mise à niveau d’un cluster, utilisez la commande [az aks get-upgrades][] pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Dans l’exemple suivant, la version actuelle du nœud est *1.9.6*, et les versions disponibles sont affichées sous la colonne *Mises à niveau*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Mettre à niveau un cluster

Utilisez la commande [az aks upgrade][] pour mettre à niveau les nœuds du cluster. Les exemples suivants mettent à jour le cluster vers la version *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

La sortie de l’exemple condensé suivant montre que *kubernetesVersion* indique maintenant *1.10.3* :

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Valider la mise à niveau

Vérifiez si la mise à niveau a réussi avec la commande [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez mis à niveau Kubernetes dans un cluster AKS. Les tâches suivantes ont été accomplies :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

Suivez ce lien pour en savoir plus sur AKS.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade