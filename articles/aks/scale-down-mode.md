---
title: Utiliser le mode Scale-down pour votre cluster AKS (Azure Kubernetes Service) (préversion)
titleSuffix: Azure Kubernetes Service
description: Découvrez comment utiliser le mode Scale-down dans AKS (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 6bd1f4c70d9c427d7f6487040453e7ecae199717
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800115"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>Utiliser le mode Scale-down pour supprimer/libérer des nœuds dans AKS (Azure Kubernetes Service) (préversion)

Par défaut, les opérations de scale-up effectuées manuellement ou via le composant de mise à l’échelle automatique de cluster nécessitent l’allocation et le provisionnement de nouveaux nœuds, alors que les opérations de scale-down entraînent la suppression de nœuds. Le mode Scale-down vous permet de décider si vous souhaitez supprimer ou libérer les nœuds de votre cluster AKS (Azure Kubernetes Service) au moment du scale-down. 

Quand une machine virtuelle Azure est à l’état `Stopped` (libéré), les ressources de calcul de la machine virtuelle ne vous sont pas facturées. Toutefois, vous devez quand même payer pour l’OS et les disques de stockage de données attachés à la machine virtuelle. Cela signifie également que les images conteneur sont conservées sur ces nœuds. Pour plus d’informations, consultez [États et facturation des machines virtuelles Azure][state-billing-azure-vm]. Ce comportement permet d’accélérer la vitesse des opérations, car votre déploiement tire profit des images mises en cache. Le mode Scale-down vous permet de ne plus avoir à préprovisionner les nœuds ni préextraire les images conteneur, ce qui vous permet de réduire les coûts de calcul.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Avant de commencer

> [!WARNING]
> Pour conserver les machines virtuelles libérées, vous devez affecter Deallocate au mode Scale-down. Cela inclut les machines virtuelles libérées à l’aide d’API IaaS (API de groupe de machines virtuelles identiques). Si vous affectez Delete au mode Scale-down, toutes les machines virtuelles libérées sont supprimées.

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limites

- Les disques d’[OS éphémère][ephemeral-os] ne sont pas pris en charge. Veillez à spécifier les disques d’OS managés via `--node-osdisk-type Managed` au moment de la création d’un cluster ou d’un pool de nœuds.
- Les [pools de nœuds spot][spot-node-pool] ne sont pas pris en charge.

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.30 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKS-ScaleDownModePreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `AKS-ScaleDownModePreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKS-ScaleDownModePreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>Utilisation du mode Scale-down pour libérer des nœuds au moment d’un scale-down

En définissant `--scale-down-mode Deallocate`, les nœuds sont libérés durant un scale-down de votre cluster/pool de nœud. Tous les nœuds libérés sont arrêtés. Quand votre cluster/pool de nœuds doit faire l’objet d’un scale-up, les nœuds libérés démarrent en premier, avant le provisionnement de nouveaux nœuds.

Dans cet exemple, nous créons un pool de nœuds comportant 20 nœuds, et nous spécifions qu’au moment du scale-down, les nœuds doivent être libérés via `--scale-down-mode Deallocate`.

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

En mettant à l’échelle le pool de nœuds et en changeant le nombre de nœuds pour n’en garder que 5, nous libérons 15 nœuds.

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>Suppression des nœuds libérés

Pour supprimer les nœuds libérés, vous pouvez faire passer le mode Scale-down à `Delete` en définissant `--scale-down-mode Delete`. Les 15 nœuds libérés vont maintenant être supprimés.

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

> [!NOTE]
> Si vous faites passer le mode Scale-down de `Deallocate` à `Delete`, puis de nouveau à `Deallocate`, tous les nœuds libérés sont supprimés, et votre pool de nœuds reste en mode Scale-down `Deallocate`.

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>Utilisation du mode Scale-down pour supprimer des nœuds au moment d’un scale-down

Sans mode Scale-down, le comportement par défaut d’AKS consiste à supprimer vos nœuds quand vous effectuez une scale-down de votre cluster. À l’aide du mode Scale-down, vous pouvez l’effectuer de manière explicite en définissant `--scale-down-mode Delete`.

Dans cet exemple, nous créons un pool de nœuds en spécifiant que les nœuds doivent être supprimés au moment du scale-down via `--scale-down-mode Delete`. Les opérations de mise à l’échelle sont gérées via le composant de mise à l’échelle automatique de cluster.

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la mise à niveau de votre cluster AKS, consultez [Mettre à niveau un cluster AKS][aks-upgrade]
- Pour en savoir plus sur le composant de mise à l’échelle automatique de cluster, consultez [Effectuer la mise à l’échelle automatique d’un cluster en réponse aux demandes des applications sur AKS][cluster-autoscaler]

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md