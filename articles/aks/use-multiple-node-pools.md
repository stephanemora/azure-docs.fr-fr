---
title: Utiliser plusieurs pools de nœuds dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 9c72c8431907c52dab338114ce09be139608ab0a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768586"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (AKS)

Dans Azure Kubernetes Service (AKS), les nœuds d’une même configuration sont regroupés dans des *pools de nœuds*. Ces pools de nœuds contiennent les machines virtuelles sous-jacentes qui exécutent vos applications. Le nombre initial de nœuds et leur taille (référence SKU) sont définis quand vous créez un cluster AKS, opération qui crée un *pool de nœuds par défaut*. Pour prendre en charge les applications qui ont des exigences de calcul ou de stockage différentes, vous pouvez créer des pools de nœuds supplémentaires. Par exemple, utilisez ces pools de nœuds supplémentaires pour fournir des GPU pour les applications nécessitant beaucoup de ressources système ou pour accéder à un stockage SSD hautes performances.

> [!NOTE]
> Cette fonctionnalité permet de mieux contrôler la création et la gestion de pools de nœuds multiples. Par conséquent, des commandes distinctes sont requises pour créer/mettre à jour/supprimer. Auparavant, les opérations de cluster via `az aks create` ou `az aks update` utilisaient l’API managedCluster et étaient la seule option pour modifier votre plan de contrôle et un pool de nœuds unique. Cette fonctionnalité expose une opération distincte définie pour les pools d’agents via l’API agentPool et requiert l’utilisation du jeu de commandes `az aks nodepool` pour exécuter des opérations sur un pool de nœuds individuel.

Cet article vous montre comment créer et gérer plusieurs pools de nœuds dans un cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

La version 2.0.76 d’Azure CLI (ou ultérieure) doit être installée et configurée. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez les clusters AKS prenant en charge plusieurs pools de nœuds :

* Vous ne pouvez pas supprimer le pool de nœuds par défaut (premier).
* Le module complémentaire de routage d’application HTTP ne peut pas être utilisé.
* Le cluster AKS doit utiliser l’équilibreur de charge de la référence SKU Standard pour utiliser plusieurs pools de nœuds, la fonctionnalité n’est pas prise en charge avec les équilibreurs de charge de la référence SKU De base.
* Le cluster AKS doit utiliser des groupes de machines virtuelles identiques pour les nœuds.
* Le nom d’un pool de nœuds ne peut contenir que des caractères alphanumériques minuscules et doit commencer par une lettre minuscule. Pour les pools de nœuds Linux, la longueur doit être comprise entre 1 et 12 caractères. Pour les pools de nœuds Windows, elle doit être comprise entre 1 et 6 caractères.
* Le cluster AKS peut avoir un maximum de huit pools de nœuds.
* Le cluster AKS peut avoir un maximum de 800 nœuds dans ces 8 pools de nœuds.
* Tous les pools de nœuds doivent résider dans le même réseau virtuel et sous-réseau.
* Lors de la création de plusieurs pools de nœuds au moment de la création du cluster, toutes les versions de Kubernetes utilisées par les pools de nœuds doivent correspondre à la version définie pour le plan de contrôle. Cela peut être mis à jour une fois que le cluster a été provisionné, en utilisant des opérations par pool de nœuds.

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Pour commencer, créez un cluster AKS avec un pool de nœuds unique. L’exemple suivant utilise la commande [az group create][az-group-create] pour créer un groupe de ressources nommé *myResourceGroup* dans la région *eastus*. Un cluster AKS nommé *myAKSCluster* est alors créé à l’aide de la commande [az aks create][az-aks-create]. Un paramètre *--kubernetes-version* de valeur *1.13.10* est utilisé pour montrer comment mettre à jour un pool de nœuds dans une étape suivante. Vous pouvez spécifier une [version Kubernetes prise en charge][supported-versions].

> [!NOTE]
> La référence SKU d’équilibreur de charge *De base***n’est pas prise en charge** en cas d’utilisation de plusieurs pools de nœuds. Par défaut, les clusters AKS sont créés avec la référence SKU d’équilibreur de charge *Standard* dans Azure CLI et le portail Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

La création du cluster ne prend que quelques minutes.

> [!NOTE]
> Pour vous assurer de la fiabilité de votre cluster, vous devez exécuter au moins 2 (deux) nœuds dans le pool de nœuds par défaut, car les services système essentiels sont exécutés sur ce pool de nœuds.

Lorsque le cluster est prêt, utilisez la commande [az aks get-credentials][az-aks-get-credentials] pour obtenir les informations d’identification du cluster à utiliser avec `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Ajouter un pool de nœuds

Le cluster créé à l’étape précédente dispose d’un pool de nœuds unique. Nous allons ajouter un deuxième pool de nœuds à l’aide de la commande [az aks nodepool add][az-aks-nodepool-add]. L’exemple suivant crée un pool de nœuds nommé *mynodepool* qui exécute *3* nœuds :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> Le nom d’un pool de nœuds doit commencer par une lettre minuscule et ne peut contenir que des caractères alphanumériques. Pour les pools de nœuds Linux, la longueur doit être comprise entre 1 et 12 caractères. Pour les pools de nœuds Windows, elle doit être comprise entre 1 et 6 caractères.

Pour afficher l’état de vos pools de nœuds, utilisez la commande [az aks node pool list][az-aks-nodepool-list] et spécifiez votre groupe de ressources et le nom du cluster :

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

L’exemple de sortie suivant montre que *mynodepool* a été créé avec trois nœuds dans le pool de nœuds. Lorsque le cluster AKS a été créé à l'étape précédente, un pool *nodepool1* par défaut a été généré avec *2* nœuds.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Si aucun paramètre *OrchestratorVersion* ou *VmSize* n’est spécifié lorsque vous ajoutez un pool de nœuds, les nœuds sont créés en fonction des valeurs par défaut pour le cluster AKS. Dans cet exemple, il s’agissait de la version Kubernetes *1.13.10* et de la taille de nœud *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Mettre à niveau un pool de nœuds
 
> [!NOTE]
> Les opérations de mise à niveau et de mise à l’échelle sur un cluster ou un pool de nœuds ne peuvent pas se produire simultanément. Toute tentative de les exécuter simultanément retourne une erreur. En effet, chaque opération doit être terminée sur la ressource cible avant l’exécution de la demande suivante sur cette même ressource. Pour en savoir plus, voir notre [Guide de résolution des problèmes](https://aka.ms/aks-pending-upgrade).

Lors de la création de votre cluster AKS à la première étape, la valeur *1.13.10* a été spécifiée pour `--kubernetes-version`. Celle-ci définit la version Kubernetes à la fois pour le plan de contrôle et le pool de nœuds par défaut. Les commandes de cette section expliquent comment mettre à niveau un seul pool de nœuds.

La relation entre la mise à niveau de la version Kubernetes du plan de contrôle et le pool de est décrite [ci-dessous](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> La version de l'image de système d'exploitation du pool de nœuds est liée à la version Kubernetes du cluster. Vous n'obtiendrez les mises à niveau de l'image de système d'exploitation qu'après une mise à niveau du cluster.

Comme il existe deux pools de nœuds dans cet exemple, nous devons utiliser la commande [az aks nodepool upgrade][az-aks-nodepool-upgrade] pour mettre à niveau un pool de nœuds. Nous allons mettre à niveau *mynodepool* vers Kubernetes *1.13.10*. Utilisez la commande [az aks nodepool upgrade][az-aks-nodepool-upgrade] pour mettre à niveau le pool de nœuds, comme dans l’exemple suivant :

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

Listez de nouveau l’état de vos pools de nœuds à l’aide de la commande [az aks node pool list][az-aks-nodepool-list]. L’exemple suivant montre que *mynodepool* est dans l’état *Mise à niveau* vers *1.13.10* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Il faut quelques minutes pour mettre à niveau les nœuds vers la version spécifiée.

En guise de bonne pratique, vous devez mettre à niveau tous les pools de nœuds dans un cluster AKS vers la même version de Kubernetes. Le comportement par défaut de `az aks upgrade` est de mettre à niveau tous les pools de nœuds en même temps que le plan de contrôle pour obtenir cet alignement. La possibilité de mettre à niveau les pools de nœuds individuels vous permet d’effectuer une mise à niveau propagée et de planifier des pods entre les pools de nœuds pour maintenir la disponibilité des applications au sein des contraintes susmentionnées.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Mettre à niveau un plan de contrôle de cluster avec plusieurs pools de nœuds

> [!NOTE]
> Kubernetes utilise le schéma de contrôle de version standard [Semantic Versioning](https://semver.org/). Le numéro de version est exprimé par *x.y.z*, où *x* est la version principale, *y* est la version secondaire et *z* est la version du correctif. Par exemple, dans la version *1.12.6*, 1 est la version principale, 12 est la version secondaire, et 6 est la version du correctif. La version Kubernetes du plan de contrôle et le pool de nœuds initial sont définis lors de la création du cluster. Tous les pools de nœuds supplémentaires ont leur version Kubernetes définie lorsqu'ils sont ajoutés au cluster. Les versions Kubernetes peuvent différer entre les pools de nœuds ainsi qu’entre un pool de nœuds et le plan de contrôle.

Un cluster AKS possède deux objets de ressource de cluster avec des versions Kubernetes associées.

1. Un plan de contrôle de cluster est une version Kubernetes du plan de contrôle.
2. Pool de nœuds avec une version de Kubernetes.

Un plan de contrôle est mappé à un ou plusieurs pools de nœuds. Le comportement d’une opération de mise à niveau dépend de la commande Azure CLI utilisée.

La mise à niveau d’un plan de contrôle AKS requiert l'utilisation de `az aks upgrade`. Cela met à niveau la version du plan de contrôle et tous les pools de nœuds dans le cluster. 

L’émission de la commande `az aks upgrade` avec l’indicateur `--control-plane-only` ne met à niveau que le plan de contrôle du cluster. Aucun des pools de nœuds associés dans le cluster n’est modifié.

La mise à niveau de pools de nœuds individuels requiert l’utilisation de `az aks nodepool upgrade`. Cette commande met uniquement à niveau le pool de nœuds cible avec la version de Kubernetes spécifiée

### <a name="validation-rules-for-upgrades"></a>Règles de validation pour les mises à niveau

Les mises à niveau valides de Kubernetes du plan de contrôle ou des pools de nœuds d’un cluster sont validées par les ensembles de règles suivants.

* Règles pour la mise à niveau des pools de nœuds par les versions valides :
   * La version du pool de nœuds doit avoir la même version *principale* que le plan de contrôle.
   * La version *secondaire* du pool de nœuds doit être située à deux versions *secondaires* de la version du plan de contrôle.
   * La version du pool de nœuds ne peut pas être supérieure à la version du `major.minor.patch` de contrôle.

* Règles pour l’envoi d’une opération de mise à niveau :
   * Vous ne pouvez pas passer à une version de Kubernetes antérieure pour le plan de contrôle ou le pool de nœuds.
   * Si une version de Kubernetes de pool de nœuds n’est pas spécifiée, le comportement dépend du client utilisé. Dans les modèles Resource Manager, la déclaration revient à la version existante définie pour le pool de nœuds si elle est utilisée ; si aucune valeur n’est définie, la version du plan de contrôle est utilisée dans le cadre du rétablissement de la version antérieure.
   * Vous pouvez procéder à la mise à niveau ou à la mise à l'échelle d'un plan de contrôle ou d'un pool de nœuds à un moment donné, mais vous ne pouvez pas soumettre plusieurs opérations sur un plan de contrôle ou une ressource de pools simultanément.

## <a name="scale-a-node-pool-manually"></a>Mettre manuellement à l'échelle un pool de nœuds

Lorsque vos exigences de charge de travail d’application changent, vous pouvez être amené à mettre à l’échelle le nombre de nœuds dans un pool de nœuds. Le nombre de nœuds peut être augmenté ou diminué.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Pour mettre à l’échelle le nombre de nœuds dans un pool de nœuds, utilisez la commande [az aks node pool scale][az-aks-nodepool-scale]. L’exemple suivant met à l’échelle le nombre de nœuds dans *mynodepool* sur *5* :

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Listez de nouveau l’état de vos pools de nœuds à l’aide de la commande [az aks node pool list][az-aks-nodepool-list]. L’exemple suivant montre que *mynodepool* est dans l’état *Upgrading (Mise à niveau)* avec une nouvelle valeur de *5* nœuds :

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

L’opération de mise à l’échelle peut prendre quelques minutes.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Mettre automatiquement à l’échelle un pool de nœuds spécifique en activant l’option de mise à l’échelle automatique de cluster

AKS offre une fonctionnalité distincte pour mettre automatiquement à l’échelle les pools de nœuds avec une fonctionnalité appelée [mise à l’échelle automatique du cluster](cluster-autoscaler.md). Cette fonctionnalité peut être activée pour chaque pool de nœuds avec un nombre minimal et maximal de mise à l’échelle par pool de nœuds. Découvrez comment [utiliser la mise à l’échelle automatique de cluster par pool de nœuds](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Supprimer un pool de nœuds

Si vous n’avez plus besoin d’un pool, vous pouvez le supprimer et supprimer les nœuds des machines virtuelles sous-jacentes. Pour supprimer un pool de nœuds, utilisez la commande [az aks node pool delete][az-aks-nodepool-delete] et spécifiez le nom du pool de nœuds. L’exemple suivant supprime le pool *mynodepool* créé au cours des étapes précédentes :

> [!CAUTION]
> Il n’existe aucune option de récupération pour les pertes de données qui peuvent se produire lorsque vous supprimez un pool de nœuds. Si des pods ne peuvent pas être planifiés sur d’autres pools de nœuds, ces applications ne sont pas disponibles. Veillez à ne pas supprimer un pool de nœuds lorsque des applications en cours d’utilisation n’ont pas de sauvegardes de données ou la possibilité de s’exécuter sur d’autres pools de nœuds dans votre cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L’exemple de sortie suivant de la commande [az aks node pool list][az-aks-nodepool-list] montre que *mynodepool* est dans l’état *Deleting (Suppression)*  :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

La suppression des nœuds et du pool de nœuds prend quelques minutes.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Spécifier une taille de machine virtuelle pour un pool de nœuds

Dans les exemples précédents de création d’un pool de nœuds, une taille de machine virtuelle par défaut a été utilisée pour les nœuds créés dans le cluster. Un scénario plus courant consiste à créer des pools de nœuds avec des fonctionnalités et des tailles de machine virtuelle différentes. Par exemple, vous pouvez créer un pool de nœuds contenant des nœuds avec de grandes quantités de mémoire ou de processeur, ou un pool de nœuds fournissant une prise en charge GPU. Dans l’étape suivante, vous allez [utiliser des teintes et des tolérances](#schedule-pods-using-taints-and-tolerations) pour indiquer au planificateur Kubernetes comment limiter l’accès aux pods qui peuvent s’exécuter sur ces nœuds.

Dans l’exemple suivant, créez un pool de nœuds basé sur GPU qui utilise la taille de machine virtuelle *Standard_NC6*. Ces machines virtuelles sont alimentées par la carte NVIDIA Tesla K80. Pour obtenir des informations sur les tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Linux dans Azure][vm-sizes].

Créez un pool de nœuds de nouveau à l’aide de la commande [az aks node pool add][az-aks-nodepool-add]. Cette fois, spécifiez le nom *gpunodepool* et utilisez le paramètre `--node-vm-size` pour spécifier la taille *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L’exemple de sortie suivant de la commande [az aks node pool list][az-aks-nodepool-list] montre que *gpunodepool* crée (*Creating*) des nœuds avec la taille *VmSize* spécifiée :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Il faut quelques minutes pour que *gpunodepool* soit créé avec succès.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Planifier des pods à l’aide de teintes et de tolérances

Vous avez maintenant deux pools de nœuds dans votre cluster, le pool de nœuds par défaut initialement créé et le pool de nœuds basé sur GPU. Utilisez la commande [kubectl get nodes][kubectl-get] pour voir les nœuds figurant dans votre cluster. L'exemple de sortie suivant montre les nœuds :

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Le planificateur Kubernetes peut utiliser des teintes et des tolérances pour restreindre les charges de travail qui peuvent s’exécuter sur des nœuds.

* Quand une **teinte** est appliquée à un nœud, seuls des pods spécifiques peuvent être planifiés sur le nœud.
* Une **tolérance** est ensuite appliquée à un pod pour lui permettre de *tolérer* la teinte d’un nœud.

Pour plus d’informations sur la façon d’utiliser les fonctionnalités de Kubernetes planifiées avancées, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][taints-tolerations].

Dans cet exemple, appliquez une teinte à votre nœud basé sur GPU à l’aide de la commande [kubectl taint node][kubectl-taint]. Spécifiez le nom de votre nœud basé sur GPU à partir de la sortie de la commande `kubectl get nodes` précédente. La teinte est appliquée en tant que *clé:valeur*, puis comme option de planification. L’exemple suivant utilise la paire *sku=gpu* et définit des pods qui ont la capacité *NoSchedule* :

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

L’exemple de manifeste YAML de base suivant utilise une tolérance pour autoriser le planificateur Kubernetes à exécuter un pod NGINX sur le nœud basé sur GPU. Pour obtenir un exemple plus approprié mais long d’exécution d’une tâche Tensorflow par rapport au jeu de données MNIST, consultez [Utiliser des GPU pour les charges de travail nécessitant beaucoup de ressources système sur AKS][gpu-cluster].

Créez un fichier nommé `gpu-toleration.yaml` et copiez-y l’exemple de code YAML suivant :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Planifiez le pod à l’aide de la commande `kubectl apply -f gpu-toleration.yaml` :

```console
kubectl apply -f gpu-toleration.yaml
```

Il faut quelques secondes pour planifier le pod et extraire l’image NGINX. Utilisez la commande [kubectl describe pod][kubectl-describe] pour voir l’état du pod. L’exemple de sortie condensée suivant montre que la tolérance *sku=gpu:NoSchedule* est appliquée. Dans la section des événements, le planificateur a affecté le pod au nœud basé sur GPU *aks-gpunodepool-28993262-vmss000000* :

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Seuls les pods qui ont cette teinte appliquée peuvent être planifiés sur les nœuds dans *gpunodepool*. N’importe quel autre pod sera planifié dans le pool de nœuds *nodepool1*. Si vous créez des pools de nœuds supplémentaires, vous pouvez utiliser des teintes et des tolérances supplémentaires pour limiter les pods qui peuvent être planifiés sur ces ressources de nœud.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gérer les pools de nœuds à l’aide d’un modèle Resource Manager

Lorsque vous utilisez un modèle Azure Resource Manager pour créer et gérer des ressources, vous pouvez généralement mettre à jour les paramètres dans votre modèle et le redéployer pour mettre à jour la ressource. Avec les pools de nœuds dans AKS, le profil de pool de nœuds initial ne peut pas être mis à jour une fois que le cluster AKS a été créé. Ce comportement signifie que vous ne pouvez pas mettre à jour un modèle Resource Manager existant, apporter un changement aux pools de nœuds et redéployer. Au lieu de cela, vous devez créer un modèle Resource Manager distinct qui mette à jour uniquement les pools de nœuds pour un cluster AKS existant.

Créez un modèle tel que `aks-agentpools.json` et collez l’exemple de manifeste suivant. Cet exemple de modèle configure les paramètres suivants :

* Met à jour le pool de nœuds *Linux* nommé *myagentpool* pour exécuter trois nœuds.
* Définit les nœuds dans le pool de nœuds pour exécuter Kubernetes version *1.13.10*.
* Définit la taille de nœud en tant que *Standard_DS2_v2*.

Modifiez ces valeurs en fonction des besoins pour mettre à jour, ajouter ou supprimer des pools de nœuds :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2019-04-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

Déployez ce modèle à l’aide de la commande [az group deployment create][az-group-deployment-create], comme indiqué dans l’exemple suivant. Vous êtes invité à fournir le nom et l’emplacement du cluster AKS existant :

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

La mise à jour de votre cluster AKS peut prendre quelques minutes selon les paramètres de pool de nœuds et les opérations que vous définissez dans votre modèle Resource Manager.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Attribuer une adresse IP publique par nœud dans un pool de nœuds

> [!WARNING]
> Pendant la préversion de l’affectation d’une adresse IP publique par nœud, elle ne peut pas être utilisée avec la *référence SKU Standard Load Balancer dans AKS* en raison des éventuelles règles d’équilibreur de charge en conflit avec le provisionnement de machine virtuelle. Pendant la période de préversion, vous devez utiliser la *référence (SKU) d’équilibreur de charge de base* si vous devez affecter une adresse IP publique par nœud.

Les nœuds AKS n’ont pas besoin de leurs propres adresses IP publiques pour communiquer. Toutefois, dans certains cas, les nœuds d’un pool de nœuds doivent avoir leurs propres adresses IP publiques. C’est par exemple le cas du gaming, où une console doit être directement connectée à une machine virtuelle dans le cloud pour réduire les tronçons. Pour ce faire, vous devez vous inscrire pour une fonctionnalité d’évaluation distincte, à savoir Node Public IP (préversion).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Après l’inscription, déployez un modèle Azure Resource Manager selon les mêmes instructions décrites [ci-dessus](#manage-node-pools-using-a-resource-manager-template) et ajoutez la propriété de valeur booléenne `enableNodePublicIP` à agentPoolProfiles. Définissez la valeur sur `true` car, par défaut, elle est définie sur `false`. Il s’agit d’une propriété de création uniquement qui requiert une version d’API minimale de 2019-06-01. Ceci peut être appliqué aux pools de nœuds Linux et Windows.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans cet article, vous avez créé un cluster AKS qui inclut des nœuds basés sur GPU. Pour réduire les coûts inutiles, vous voudrez peut-être supprimer le pool *gpunodepool* ou le cluster AKS tout entier.

Pour supprimer le pool de nœuds basé sur GPU, utilisez la commande [az aks nodepool delete][az-aks-nodepool-delete] comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Pour supprimer le cluster lui-même, utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources AKS :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer et gérer plusieurs pools de nœuds dans un cluster AKS. Pour plus d’informations sur la façon de contrôler les pods entre les pools de nœuds, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][operator-best-practices-advanced-scheduler].

Pour créer et utiliser des pools de nœuds de conteneur Windows Server, consultez [Créer un conteneur Windows Server dans AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
