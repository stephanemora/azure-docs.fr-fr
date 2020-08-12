---
title: Mise à jour d’un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment mettre à niveau un cluster Azure Kubernetes service (AKS) pour obtenir les fonctionnalités et mises à jour de sécurité les plus récentes.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050626"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Mise à jour d’un cluster Azure Kubernetes Service (AKS)

Dans le cadre du cycle de vie d’un cluster AKS, vous devez souvent opérer une mise à niveau vers la dernière version de Kubernetes. Il est important d’appliquer les dernières publications de sécurité de Kubernetes, ou d’opérer une mise à niveau pour obtenir les dernières fonctionnalités. Cet article vous montre comment mettre à niveau les composants principaux ou un pool de nœud unique par défaut dans un cluster AKS.

Pour en savoir plus sur les clusters AKS utilisant plusieurs pools de nœuds ou des nœuds Windows Server, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Avant de commencer

Pour les besoins de cet article, vous devez utiliser Azure CLI version 2.0.65 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

> [!WARNING]
> Une mise à niveau de cluster AKS déclenche une isolation et un drainage de vos nœuds. Si vous n’avez qu’un quota de calcul faible, la mise à niveau peut échouer. Pour plus d’informations, consultez l’article [Augmenter les quotas](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="check-for-available-aks-cluster-upgrades"></a>Recherchez les mises à niveau du cluster AKS disponibles

Pour rechercher les publications de Kubernetes disponibles pour votre cluster, utilisez la commande [az aks get-upgrades][az-aks-get-upgrades]. L’exemple suivant recherche les mises à niveau disponibles pour le cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Lors de la mise à niveau d’un cluster AKS pris en charge, les versions mineures de Kubernetes ne peuvent pas être ignorées. Par exemple, les mises à niveau *1.12.x* -> *1.13.x* ou *1.13.x* -> *1.14.x* sont autorisées, mais pas *1.12.x* -> *1.14.x*.
>
> Pour opérer une mise à niveau *1.12.x* -> *1.14.x*, commencez par une mise à niveau *1.12.x* -> *1.13.x*, puis effectuez la mise à niveau *1.13.x* -> *1.14.x*.
>
> L’omission de plusieurs versions ne peut être effectuée que lors de la mise à niveau d’une version non prise en charge vers une version prise en charge. Par exemple, la mise à niveau à partir d’une version *1.10.x* non prise en charge --> une version *1.15.x* prise en charge peut être effectuée.

L’exemple de sortie suivant montre que le cluster peut être mis à niveau vers les versions *1.13.9* et *1.13.10* :

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Si aucune mise à niveau n’est disponible, vous obtenez :
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>Personnaliser l’augmentation du nombre de nœuds pendant une mise à niveau (préversion)

> [!Important]
> Une augmentation du nombre de nœuds nécessite un quota d’abonnement pour l’augmentation maximale (max-surge) demandée pour chaque opération de mise à niveau. Par exemple, un cluster comprenant 5 pools de 4 nœuds a 20 nœuds en tout. Si chaque pool de nœuds a une valeur max-surge de 50 %, un quota de calcul et d’adresse IP supplémentaire de 10 nœuds (2 nœuds * 5 pools) est nécessaire pour effectuer la mise à niveau.
>
> Si vous utilisez Azure CNI, vérifiez que des adresses IP sont disponibles dans le sous-réseau et assurez-vous de [satisfaire aux exigences IP d’Azure CNI](configure-azure-cni.md).

Par défaut, AKS configure les mises à niveau avec un nœud supplémentaire. La valeur par défaut d’un nœud pour le paramètre max-surge permet à AKS de réduire les interruptions de la charge de travail en créant un nœud supplémentaire avant l’isolation ou le drainage des applications existantes pour remplacer un nœud d’une version antérieure. La valeur max-surge peut être personnalisée par pool de nœuds pour obtenir un compromis entre la vitesse de mise à niveau et l’interruption causée par la mise à niveau. En augmentant la valeur max-surge, le processus de mise à niveau se termine plus rapidement, bien que celui-ci puisse faire l’objet d’interruptions. 

Par exemple, une valeur max-surge de 100 % offre le processus de mise à niveau le plus rapide possible (doublant le nombre de nœuds), mais entraîne également la vidange simultanée de tous les nœuds du pool de nœuds. Il est donc préférable d’utiliser des valeurs élevées telles que celle-ci pour les environnements de test. Pour les pools de nœuds de production, nous vous recommandons d’utiliser un paramètre max-surge de 33 %.

AKS accepte à la fois des valeurs entières et des valeurs en pourcentage pour max-surge. Ainsi, l’entier « 5 » indique une augmentation de cinq nœuds. La valeur « 50 % » indique une augmentation égale à la moitié du nombre de nœuds actuel dans le pool. Les valeurs en pourcentage de max-surge vont de 1 % (minimum) à 100 % (maximum). Si vous utilisez une valeur en pourcentage, le nombre de nœuds est arrondi à la valeur entière la plus proche. Si la valeur max-surge est inférieure au nombre de nœuds actuel au moment de la mise à niveau, le nombre de nœuds actuel est utilisé comme valeur max-surge.

Pendant une mise à niveau, la valeur max-surge peut être égale au minimum à 1 et au maximum au nombre de nœuds dans votre pool de nœuds. Vous pouvez définir des valeurs plus élevées, mais le nombre maximal de nœuds utilisés pour max-surge ne sera pas supérieur au nombre de nœuds dans le pool au moment de la mise à niveau.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Configurer la fonctionnalité en préversion pour personnaliser l’augmentation du nombre de nœuds pendant une mise à niveau

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

L’inscription prend plusieurs minutes. Utilisez la commande ci-dessous pour vérifier que la fonctionnalité est inscrite :

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

Durant la préversion, vous avez besoin de l’extension CLI *aks-preview* pour utiliser max-surge. Utilisez la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Le paramètre max-surge sur un pool de nœuds est permanent.  Les mises à niveau ultérieures de Kubernetes ou les mises à niveau des versions de nœud utiliseront ce paramètre. Vous pouvez modifier la valeur de max-surge pour vos pools de nœuds à tout moment. Pour les pools de nœuds de production, nous vous recommandons de définir un paramètre max-surge de 33 %.

Utilisez les commandes suivantes pour définir des valeurs max-surge pour des pools de nœuds nouveaux ou existants.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Mettre à niveau un cluster AKS

Avec une liste des versions disponibles pour votre cluster AKS, utilisez la commande [az aks upgrade][az-aks-upgrade] pour opérer la mise à niveau. Pendant le processus de mise à niveau, AKS ajoute un nouveau nœud au cluster exécutant la version de Kubernetes indiquée, puis il [isole et draine][kubernetes-drain] précautionneusement l’un des anciens nœuds afin de perturber le moins possible les applications en cours d’exécution. Une fois que l’exécution des pods d’application par le nouveau nœud est confirmée, l’ancien nœud est supprimé. Ce processus se répète jusqu’à ce que tous les nœuds du cluster soient mis à niveau.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Quelques minutes suffisent pour mettre à niveau le cluster. Le temps nécessaire varie en fonction du nombre de nœuds.

> [!NOTE]
> Il existe un temps total alloué à la mise à niveau de cluster. Cette durée est calculée en prenant le produit de l’opération `10 minutes * total number of nodes in the cluster`. Par exemple, dans un cluster de 20 nœuds, les opérations de mise à niveau doivent réussir en 200 minutes. Dans le cas contraire, AKS fait échouer l’opération pour éviter un état de cluster irrécupérable. Pour effectuer une récupération en cas d’échec de mise à niveau, retentez l’opération de mise à niveau une fois le délai d’expiration atteint.

Pour vérifier si la mise à niveau a réussi, utilisez la commande [az aks show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L’exemple de sortie suivant montre que le cluster exécute à présent *1.13.10* :

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
