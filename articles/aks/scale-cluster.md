---
title: Mettre à l’échelle un cluster Azure Kubernetes Service (AKS)
description: Apprenez à mettre le nombre de nœuds à l'échelle dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/10/2019
ms.author: iainfoulds
ms.openlocfilehash: 558a3b6dc15293ab9a0895aa4f9f709ba2d0a51f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214621"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Mettre le nombre de nœuds à l'échelle dans un cluster Azure Kubernetes Service (AKS)

Si les besoins en ressources de vos applications évoluent, vous pouvez procéder manuellement à la mise à l'échelle d'un cluster AKS pour exécuter un nombre de nœuds différent. Si vous réduisez l'échelle, les nœuds sont soigneusement [coordonnés et purgés][kubernetes-drain] afin de limiter les perturbations pour les applications en cours d'exécution. Si vous augmentez l'échelle, la commande `az` attend que les nœuds soient marqués `Ready` par le cluster Kubernetes.

## <a name="scale-the-cluster-nodes"></a>Mettre à l’échelle les nœuds de cluster

Commencez par vous procurer le *nom* de votre pool de nœuds à l'aide de la commande [az aks show][az-aks-show]. Dans l'exemple suivant, cette commande permet d'obtenir le nom du pool de nœuds du cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup* :

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

Utilisez la commande `az aks scale` pour mettre à l’échelle les nœuds du cluster. L’exemple suivant met à l’échelle un cluster nommé *myAKSCluster* vers un nœud unique. Indiquez le nom du pool de nœuds (*--nodepool-name*) obtenu à l'aide de la commande précédente, par exemple *nodepool1* :

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
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": true,
  "fqdn": "myaksclust-myresourcegroup-19da35-0d60b16a.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.9.11",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "[...]"
        }
      ]
    }
  },
  "location": "eastus",
  "name": "myAKSCluster",
  "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "networkPlugin": "kubenet",
    "networkPolicy": null,
    "podCidr": "10.244.0.0/16",
    "serviceCidr": "10.0.0.0/16"
  },
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_eastus",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "servicePrincipalProfile": {
    "clientId": "[...]",
    "secret": null
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déploiement et la gestion d’ACS avec les didacticiels ACS.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
