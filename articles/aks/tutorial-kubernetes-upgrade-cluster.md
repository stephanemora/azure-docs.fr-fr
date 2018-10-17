---
title: Didacticiel Kubernetes sur Azure - Mettre à niveau un cluster
description: Dans le cadre de ce didacticiel Azure Kubernetes Service (AKS), vous allez apprendre à mettre à niveau un cluster AKS existant vers la dernière version Kubernetes disponible.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c0710be11b95b66d16661b5aff9cbf739ccda92
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901927"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutoriel : mettre à niveau Kubernetes dans Azure Kubernetes Service (AKS)

Durant le cycle de vie des applications et des clusters, vous pouvez choisir d’effectuer une mise à niveau vers la dernière version disponible de Kubernetes afin d’en exploiter les nouvelles fonctionnalités. Un cluster Azure Kubernetes Service (AKS) peut être mis à niveau à l’aide d’Azure CLI. Pour limiter les perturbations pour les applications en cours d’exécution, les nœuds Kubernetes sont soigneusement [coordonnés et vidés][kubernetes-drain] pendant le processus de mise à niveau.

Dans ce tutoriel (le septième d’une série de sept), un cluster Kubernetes est mis à niveau. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Tutoriel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

Ce didacticiel nécessite l’exécution de l’interface de ligne de commande Azure (Azure CLI) version 2.0.44 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Obtenir les versions de cluster disponibles

Avant de mettre à niveau un cluster, utilisez la commande [az aks get-upgrades][] pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau :

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

Dans l’exemple ci-après, la version actuelle est *1.9.6*, et les versions disponibles apparaissent sous la colonne *Upgrades*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Mettre à niveau un cluster

Utilisez la commande [az aks upgrade][] pour mettre à niveau le cluster AKS. L’exemple ci-après met à niveau le cluster vers la version Kubernetes *1.10.6*.

> [!NOTE]
> Vous ne pouvez mettre à niveau qu’une version mineure à la fois. Par exemple, vous pouvez mettre à niveau la version *1.9.6* vers la version *1.10.3*, mais non la version *1.9.6* directement vers la version *1.11.x*. Pour effectuer une mise à niveau de *1.9.6* vers *1.11.x*, commencez par mettre à niveau la version *1.9.6* vers la version *1.10.3*, puis mettez à niveau la version *1.10.3* vers la version *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

L’exemple de sortie condensée ci-après indique que la version *kubernetesVersion* présente à présent la valeur *1.10.6* :

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
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Valider une mise à niveau

Assurez-vous que la mise à niveau a réussi en utilisant la commande [az aks show][] comme suit :

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie ci-après indique que le cluster AKS exécute la version *KubernetesVersion 1.10.6* :

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Supprimer le cluster

S’agissant de la dernière partie de la série de didacticiels, vous souhaiterez peut-être supprimer le cluster AKS. Les nœuds Kubernetes étant exécutés sur des machines virtuelles Azure, ils continuent d’occasionner des frais même si vous n’utilisez pas le cluster. Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service conteneur, ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations principales et suppression du principal de service AKS][sp-delete].

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez mis à niveau Kubernetes dans un cluster AKS. Vous avez appris à effectuer les actions suivantes :

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
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
