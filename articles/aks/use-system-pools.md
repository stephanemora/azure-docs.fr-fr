---
title: Utiliser des pools de nœuds système dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer et gérer des pools de nœuds système dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 04322bdaa2e0e72c5fbdbadb07f2608ee360e1e3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790556"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gérer des pools de nœuds système dans Azure Kubernetes Service (AKS)

Dans Azure Kubernetes Service (AKS), les nœuds d’une même configuration sont regroupés dans des *pools de nœuds*. Les pools de nœuds contiennent les machines virtuelles sous-jacentes qui exécutent vos applications. Les pools de nœuds système et les pools de nœuds utilisateur sont deux modes de pool de nœuds différents pour vos clusters AKS. Les pools de nœuds système sont principalement utilisés pour héberger des pods système critiques, tels que CoreDNS et tunnelfront. Les pools de nœuds utilisateur sont principalement utilisés pour héberger vos pods d'application. Cela étant, les pods d’application peuvent être planifiés sur des pools de nœuds système si vous souhaitez n'avoir qu’un pool dans votre cluster AKS. Chaque cluster AKS doit contenir au moins un pool de nœuds système avec au moins un nœud. 

> [!Important]
> Si vous exécutez un pool de nœuds système unique pour votre cluster AKS dans un environnement de production, nous vous recommandons d’utiliser au moins trois nœuds pour le pool de nœuds.

## <a name="before-you-begin"></a>Avant de commencer

* La version 2.3.1 (ou ultérieure) d’Azure CLI doit être installée et configurée. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez les clusters AKS prenant en charge les pools de nœuds système.

* Voir [Quotas, restrictions de taille de machine virtuelle et disponibilité des régions dans Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Le cluster AKS doit être élaboré avec des groupes de machines virtuelles identiques en tant que type de machine virtuelle.
* Le nom d’un pool de nœuds ne peut contenir que des caractères alphanumériques minuscules et doit commencer par une lettre minuscule. Pour les pools de nœuds Linux, la longueur doit être comprise entre 1 et 12 caractères. Pour les pools de nœuds Windows, la longueur doit être comprise entre 1 et 6 caractères.
* Pour définir un mode de pool de nœuds, vous devez utiliser l’API version 2020-03-01 ou ultérieure.
* Le mode d’un pool de nœuds est une propriété obligatoire qui doit être définie explicitement si vous utilisez des modèles ARM ou des appels d’API directs.

## <a name="system-and-user-node-pools"></a>Pools de nœuds système et utilisateur

Les nœuds du pool de nœuds système ont tous l’étiquette **kubernetes.azure.com/mode: system**. Chaque cluster AKS contient au moins un pool de nœuds système. Les pools de nœuds système présentent les restrictions suivantes :

* Le paramètre osType des pools de nœuds système doit être Linux.
* Le paramètre osType des pools de nœuds utilisateur peut être Linux ou Windows.
* Les pools de nœuds système doivent contenir au moins un nœud, et les pools de nœuds utilisateur peuvent contenir zéro, un ou plusieurs nœuds.
* Les pools de nœuds système nécessitent une référence SKU de machine virtuelle d’au moins 2 processeurs virtuels et de 4 Go de mémoire.
* Les pools de nœuds système doivent prendre en charge au moins 30 pods, comme décrit par la [formule des valeurs minimale et maximale pour les pods][maximum-pods].
* Les pools de nœuds Spot nécessitent des pools de nœuds utilisateur.

Vous pouvez effectuer les opérations suivantes avec des pools de nœuds :

* Modifiez un pool de nœuds système en pool de nœuds utilisateur, à condition de disposer d’un autre pool de nœuds système pour prendre sa place dans le cluster AKS.
* Modifiez un pool de nœuds utilisateur en pool de nœuds système.
* Supprimez des pools de nœuds utilisateur.
* Vous pouvez supprimer un pool de nœuds système à condition de disposer d’un autre pool de nœuds système pour prendre sa place dans le cluster AKS.
* Un cluster AKS peut avoir plusieurs pools de nœuds système et nécessite au moins un pool de nœuds système.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Créer un cluster AKS avec un pool de nœuds système

Lorsque vous créez un cluster AKS, vous créez automatiquement un pool de nœuds système avec un nœud unique. Par défaut, le pool de nœuds initial est en mode de type système. Lorsque vous créez des pools de nœuds avec az aks nodepool add, ces pools de nœuds sont des pools de nœuds utilisateur, sauf si vous spécifiez explicitement le paramètre mode.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *USA Est*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS. L’exemple suivant crée un cluster nommé *myAKSCluster* avec un pool système contenant un seul nœud. Pour vos charges de travail de production, assurez-vous d’utiliser des pools de nœuds système avec au moins trois nœuds. Cette opération peut prendre plusieurs minutes.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Ajouter un pool de nœuds système à un cluster AKS existant

Vous pouvez ajouter un ou plusieurs pools de nœuds système à des clusters AKS existants. La commande suivante ajoute un pool de nœuds en mode de type système avec trois nœuds, par défaut.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Afficher les détails de votre pool de nœuds

Vous pouvez vérifier les détails de votre pool de nœuds à l’aide de la commande suivante.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Un mode de type **Système** est défini pour les pools de nœuds système, et un mode de type **Utilisateur** est défini pour les pools de nœuds utilisateur.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Mettre à jour les pools de nœuds système et utilisateur

Vous pouvez modifier les modes pour les pools de nœuds système et utilisateur. Vous pouvez modifier un pool de nœuds système en pool de nœuds utilisateur seulement si un autre pool de nœuds système existe déjà sur le cluster AKS.

Cette commande modifie un pool de nœuds système en pool de nœuds utilisateur.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Cette commande modifie un pool de nœuds utilisateur en pool de nœuds système.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Supprimer un pool de nœuds système

> [!Note]
> Pour utiliser des pools de nœuds système sur des clusters AKS avant l’API version 2020-03-02, ajoutez un nouveau pool de nœuds système, puis supprimez le pool de nœuds par défaut d’origine.

Avant, vous ne pouviez pas supprimer le pool de nœuds système, qui était le pool de nœuds par défaut initial dans un cluster AKS. Vous avez maintenant la possibilité de supprimer n’importe quel pool de nœuds de vos clusters. Comme les clusters AKS nécessitent au moins un pool de nœuds système, vous devez disposer d’au moins deux pools de nœuds système sur votre cluster AKS pour pouvoir en supprimer un.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer et gérer des pools de nœuds système dans un cluster AKS. Pour plus d’informations sur l’utilisation de plusieurs pools de nœuds, consultez [Utiliser plusieurs pools de nœuds][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node