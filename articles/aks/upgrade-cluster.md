---
title: Mise à jour d’un cluster Azure Kubernetes Service (AKS)
description: Mise à jour d’un cluster Azure Kubernetes Service (AKS)
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 3fae465512501b0642d0215df7b73c9879a1a3a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Mise à jour d’un cluster Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) facilite effectuer des tâches de gestion courantes, y compris la mise à niveau Kubernetes clusters.

## <a name="upgrade-an-aks-cluster"></a>Mettre à niveau un cluster AKS

Avant la mise à niveau d’un cluster, utilisez la commande `az aks get-upgrades` pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Nous avons trois versions disponibles pour la mise à niveau : 1.9.1, 1.9.2 et 1.9.6. Nous pouvons utiliser la commande `az aks upgrade` pour mettre à niveau vers la dernière version disponible.  Pendant le processus de mise à niveau, les nœuds sont soigneusement [coordonnés et purgés][kubernetes-drain] afin de limiter les perturbations pour les applications en cours d’exécution.  Avant d’effectuer une mise à niveau de cluster, assurez-vous de disposer d’une capacité de calcul supplémentaire suffisante pour gérer votre charge de travail au fur et à mesure que des noeuds de cluster sont ajoutés et supprimés.

> [!NOTE]
> Lors de la mise à niveau d’un cluster AKS, les versions mineures de Kubernetes ne peuvent pas être ignorées. Par exemple, la mise à niveau entre 1.7.x > 1.8.x ou 1.8.x > 1.9.x est autorisée, toutefois 1.7 > 1.9 ne l’est pas.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
```

Output:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.9.6",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

Vérifiez si la mise à niveau a réussi avec la commande `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déploiement et la gestion d’ACS avec les didacticiels ACS.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md