---
title: Didacticiel Kubernetes sur Azure - Mettre à niveau un cluster
description: Dans le cadre de ce didacticiel Azure Kubernetes Service (AKS), vous allez apprendre à mettre à niveau un cluster AKS existant vers la dernière version Kubernetes disponible.
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4751081d628f3ea1bff411b5e391377e8b771939
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697752"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutoriel : Mettre à niveau Kubernetes dans Azure Kubernetes Service (AKS)

Durant le cycle de vie des applications et des clusters, vous pouvez choisir d’effectuer une mise à niveau vers la dernière version disponible de Kubernetes afin d’en exploiter les nouvelles fonctionnalités. Un cluster Azure Kubernetes Service (AKS) peut être mis à niveau à l’aide d’Azure CLI.

Dans ce tutoriel (le septième d’une série de sept), un cluster Kubernetes est mis à niveau. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les tutoriels précédents, une application a été empaquetée dans une image conteneur. Cette image a été chargée dans Azure Container Registry et vous avez créé un cluster AKS. L’application a ensuite été déployée sur le cluster AKS. Si vous n’avez pas effectué ces étapes et si vous souhaitez suivre cette procédure, commencez par [Tutoriel 1 : Créer des images conteneur][aks-tutorial-prepare-app].

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ce tutoriel nécessite l’exécution de l’interface de ligne de commande Azure (Azure CLI) version 2.0.53 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Ce tutoriel nécessite que vous exécutiez Azure PowerShell version 5.9.0 ou ultérieure. Exécutez `Get-InstalledModule -Name Az` pour trouver la version. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez [Installer Azure PowerShell][azure-powershell-install].

---

## <a name="get-available-cluster-versions"></a>Obtenir les versions de cluster disponibles

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Avant de mettre à niveau un cluster, utilisez la commande [az aks get-upgrades][] pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau :

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Dans l’exemple suivant, la version actuelle est *1.18.10*, et les versions disponibles s’affichent sous *Mises à niveau*.

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Avant de mettre à niveau un cluster, utilisez la cmdlet [Get-AzAksCluster][get-azakscluster] pour déterminer la version de Kubernetes que vous exécutez et la région dans laquelle elle se trouve :

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, KubernetesVersion, Location
```

Dans l’exemple suivant, la version actuelle est *1.19.9* :

```output
Name         KubernetesVersion Location
----         ----------------- --------
myAKSCluster 1.19.9            eastus
```

Utilisez la cmdlet [Get-AzAksVersion][get-azaksversion] pour déterminer les versions de mise à niveau de Kubernetes disponibles dans la région où se trouve votre cluster AKS :

```azurepowershell
Get-AzAksVersion -Location eastus | Where-Object OrchestratorVersion -gt 1.19.9
```

Les versions disponibles sont affichées sous *OrchestratorVersion*.

```output
OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.2
DefaultProperty     :
IsPreview           :
Upgrades            : {Microsoft.Azure.Commands.Aks.Models.PSOrchestratorProfile}

OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.5
DefaultProperty     :
IsPreview           :
Upgrades            : {}
```

---

## <a name="upgrade-a-cluster"></a>Mettre à niveau un cluster

Afin de limiter les perturbations pour les applications en cours d’exécution, les nœuds AKS sont soigneusement isolés et drainés. Dans ce processus, les étapes suivantes sont à exécuter :

1. Le planificateur Kubernetes empêche la planification de pods supplémentaires sur un nœud qui doit être mis à niveau.
1. Les pods en cours d’exécution sur le nœud sont planifiés sur d’autres nœuds du cluster.
1. Un nœud exécutant les derniers composants Kubernetes est créé.
1. Quand le nouveau nœud est prêt et est joints au cluster, le planificateur Kubernetes commence à exécuter des pods dessus.
1. L’ancien nœud est supprimé, et le nœud suivant du cluster commence le processus d’isolation et de drainage.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az aks upgrade][] pour mettre à niveau le cluster AKS.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> Vous ne pouvez mettre à niveau qu’une version mineure à la fois. Par exemple, vous pouvez effectuer une mise à niveau de la version *1.14.x* vers la version *1.15.x*, mais pas de la version *1.14.x* directement vers la version *1.16.x*. Pour effectuer une mise à niveau de *1.14.x* vers *1.16.x*, commencez par mettre à niveau la version *1.14.x* vers la version *1.15.x*, puis mettez à niveau la version *1.15.x* vers la version *1.16.x*.

L’exemple de sortie condensée suivant montre le résultat de la mise à niveau vers *1.19.1*. Observez que *kubernetesVersion* indique à présent *1.19.1* :

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
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utilisez la cmdlet [Set-AzAksCluster][set-azakscluster] pour mettre à niveau le cluster AKS.

```azurepowershell
Set-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -KubernetesVersion <KUBERNETES_VERSION>
```

> [!NOTE]
> Vous ne pouvez mettre à niveau qu’une version mineure à la fois. Par exemple, vous pouvez effectuer une mise à niveau de la version *1.14.x* vers la version *1.15.x*, mais pas de la version *1.14.x* directement vers la version *1.16.x*. Pour effectuer une mise à niveau de *1.14.x* vers *1.16.x*, commencez par mettre à niveau la version *1.14.x* vers la version *1.15.x*, puis mettez à niveau la version *1.15.x* vers la version *1.16.x*.

L’exemple de sortie condensée suivant montre le résultat de la mise à niveau vers la version *1.19.9*. Observez que *kubernetesVersion* indique à présent *1.20.2* :

```output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.2
PrivateFQDN             :
AgentPoolProfiles       : {default}
Name                    : myAKSCluster
Type                    : Microsoft.ContainerService/ManagedClusters
Location                : eastus
Tags                    : {}
```

---

## <a name="validate-an-upgrade"></a>Valider une mise à niveau

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Assurez-vous que la mise à niveau a réussi en utilisant la commande [az aks show][] comme suit :

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie suivant indique que le cluster AKS exécute *KubernetesVersion 1.19.1* :

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Confirmez que la mise à niveau a réussi en utilisant la cmdlet [Get-AzAksCluster][get-azakscluster] comme suit :

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, Location, KubernetesVersion, ProvisioningState
```

L’exemple de sortie suivant indique que le cluster AKS exécute *KubernetesVersion 1.20.2* :

```output
Name         Location KubernetesVersion ProvisioningState
----         -------- ----------------- -----------------
myAKSCluster eastus   1.20.2            Succeeded
```

---

## <a name="delete-the-cluster"></a>Supprimer le cluster

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Comme ce tutoriel est la dernière partie de la série, vous souhaiterez peut-être supprimer le cluster AKS. Les nœuds Kubernetes étant exécutés sur des machines virtuelles Azure, ils continuent d’occasionner des frais même si vous n’utilisez pas le cluster. Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service conteneur ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Comme ce tutoriel est la dernière partie de la série, vous souhaiterez peut-être supprimer le cluster AKS. Les nœuds Kubernetes étant exécutés sur des machines virtuelles Azure, ils continuent d’occasionner des frais même si vous n’utilisez pas le cluster. Utilisez la cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] pour supprimer le groupe de ressources, le service conteneur et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

---

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete]. Si vous avez utilisé une identité managée, l’identité est managée par la plateforme et ne vous demande pas de provisionner ou de permuter des secrets.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez mis à niveau Kubernetes dans un cluster AKS. Vous avez appris à :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

Pour plus d’informations sur AKS, consultez [Vue d’ensemble d’AKS][aks-intro]. Pour obtenir des conseils sur la création de solutions complètes avec AKS, consultez [Conseils pour les solutions AKS][aks-solution-guidance].

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
