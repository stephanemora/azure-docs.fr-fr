---
title: Utiliser plusieurs pools de nœuds dans Azure Kubernetes Service (ACS)
description: Découvrez comment créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 4086b73313d563afaecad9b6a9289905d7085004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142638"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Afficher un aperçu : créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (ACS)

Dans Azure Kubernetes Service (ACS), les nœuds de la même configuration sont regroupées dans *pools de nœuds*. Ces pools de nœuds contiennent les machines virtuelles sous-jacentes qui exécutent vos applications. Le nombre initial de nœuds et leur taille (SKU) est défini lorsque vous créez un cluster AKS, ce qui crée un *nœud pool par défaut*. Pour prendre en charge les applications qui ont de calcul différents ou des demandes de stockage, vous pouvez créer des pools de nœuds supplémentaires. Par exemple, utiliser ces pools de nœuds supplémentaires pour fournir des GPU pour les applications de calcul intensif ou l’accès à un stockage SSD hautes performances.

Cet article vous montre comment créer et gérer plusieurs pools de nœuds dans un cluster AKS. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont libre-service et participer. Les préversions sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Toutefois, ils ne sont pas pris en charge par le support technique Azure. Si vous créez un cluster, ou ajoutez ces fonctionnalités à des clusters existants, ce cluster est non pris en charge jusqu'à ce que la fonctionnalité n’est plus disponible en version préliminaire et atteignent à la disposition générale (GA).
>
> Si vous rencontrez des problèmes avec les fonctionnalités en version préliminaire, [de signaler un problème sur le référentiel GitHub d’AKS][aks-github] par le nom de la fonctionnalité d’aperçu dans le titre du bogue.

## <a name="before-you-begin"></a>Avant de commencer

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Les commandes CLI pour créer et gérer plusieurs pools de nœud sont disponibles dans le *aks-preview* extension CLI. Installer le *aks-preview* extension Azure CLI à l’aide du [az extension ajoutez] [ az-extension-add] commande, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si vous avez installé précédemment le *aks-preview* extension, installez toutes des mises à jour à l’aide de la `az extension update --name aks-preview` commande.

### <a name="register-multiple-node-pool-feature-provider"></a>Inscrire le fournisseur de fonctionnalités de plusieurs nœud pool

Pour créer un cluster AKS qui peut utiliser plusieurs pools de nœuds, tout d’abord activer deux indicateurs de fonctionnalités sur votre abonnement. Clusters de plusieurs nœuds pool utilisent un jeu de mise à l’échelle de machine virtuelle (VMSS) pour gérer le déploiement et la configuration des nœuds Kubernetes. Inscrire le *MultiAgentpoolPreview* et *VMSSPreview* indicateurs de fonctionnalités à l’aide de la [register de fonctionnalité az] [ az-feature-register] commande comme indiqué dans le exemple suivant :

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> N’importe quel cluster ACS que vous créez une fois que vous avez correctement inscrit le *MultiAgentpoolPreview* utiliser cette expérience de cluster en préversion. Pour continuer à créer des clusters normales et entièrement pris en charge, n’activez pas les fonctionnalités en version préliminaire sur les abonnements de production. Utilisez un test distinct ou un abonnement Azure de développement pour le test des fonctionnalités en version préliminaire.

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez les clusters AKS prenant en charge plusieurs pools de nœud :

* Plusieurs pools de nœud sont uniquement disponibles pour les clusters créés une fois que vous avez correctement inscrit le *MultiAgentpoolPreview* et *VMSSPreview* fonctionnalités pour votre abonnement. Impossible d’ajouter ou de gérer des pools de nœuds avec un cluster ACS existant créé avant que ces fonctionnalités ont été correctement inscrits.
* Vous ne pouvez pas supprimer le premier pool de nœud.
* Le module complémentaire de routage application HTTP ne peut pas être utilisé.

Bien que cette fonctionnalité est disponible en version préliminaire, les limitations supplémentaires suivantes s’appliquent :

* Le cluster AKS peut avoir un maximum de huit pools de nœuds.
* Le cluster AKS peut avoir un maximum de 400 nœuds dans ces pools de huit nœuds.

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Pour commencer, créez un cluster AKS avec un pool de nœud unique. L’exemple suivant utilise le [az groupe créer] [ az-group-create] commande pour créer un groupe de ressources nommé *myResourceGroup* dans le *eastus* région. Un cluster AKS nommé *myAKSCluster* est ensuite créé à l’aide de la [créer az aks] [ az-aks-create] commande. Un *--kubernetes-version* de *1.12.6* sert à montrer comment mettre à jour un pool de nœuds dans une étape suivante. Vous pouvez spécifier une [pris en charge de la version de Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

La création du cluster ne prend que quelques minutes.

Lorsque le cluster est prêt, utilisez le [az aks get-credentials] [ az-aks-get-credentials] commande pour obtenir les informations d’identification du cluster pour une utilisation avec `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Ajouter un pool de nœuds

Le cluster créé à l’étape précédente dispose d’un pool de nœud unique. Nous allons ajouter un deuxième nœud pool à l’aide de la [ajouter des nœuds du pool de az aks] [ az-aks-nodepool-add] commande. L’exemple suivant crée un pool de nœud nommé *mynodepool* qui exécute *3* nœuds :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Pour afficher l’état de vos pools de nœud, utilisez le [liste du pool de nœud az aks] [ az-aks-nodepool-list] commande et spécifiez le nom de groupe et de cluster de votre ressource :

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

L’exemple de sortie suivant montre que *mynodepool* a été créé avec trois nœuds dans le pool de nœud. Lorsque le cluster AKS a été créé à l’étape précédente, une valeur par défaut *nodepool1* a été créé avec un nombre de nœuds de *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Si aucun *OrchestratorVersion* ou *VmSize* est spécifié lorsque vous ajoutez un pool de nœuds, les nœuds sont créés selon les valeurs par défaut pour le cluster AKS. Dans cet exemple, qui était la version de Kubernetes *1.12.6* et taille de nœud *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Mise à niveau d’un pool de nœuds

Création de votre cluster AKS dans la première étape, un `--kubernetes-version` de *1.12.6* a été spécifié. Nous allons mettre à niveau le *mynodepool* à Kubernetes *1.12.7*. Utilisez le [az KS nœud pool upgrade] [ az-aks-nodepool-upgrade] commande pour mettre à niveau le pool de nœud, comme illustré dans l’exemple suivant :

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Répertorie l’état de vos pools de nœud à l’aide de la [liste du pool de nœud az aks] [ az-aks-nodepool-list] commande. L’exemple suivant montre que *mynodepool* est dans le *la mise à niveau* état *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Il prend quelques minutes pour mettre à niveau les nœuds à la version spécifiée.

Comme meilleure pratique, vous devez mettre à niveau tous les pools de nœuds dans un cluster ACS vers la même version de Kubernetes. La possibilité de mettre à niveau les pools de nœuds individuels vous permet d’effectuer une mise à niveau propagée et de planifier les pods entre des pools de nœuds pour maintenir la disponibilité des applications.

## <a name="scale-a-node-pool"></a>Mise à l’échelle un pool de nœuds

En tant que votre application charge de travail requiert la modification, vous devrez peut-être mettre à l’échelle le nombre de nœuds dans un pool de nœuds. Le nombre de nœuds peut être mis à l’échelle vers le haut ou vers le bas.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Pour faire évoluer le nombre de nœuds dans un pool de nœuds, utilisez la [à l’échelle du pool az aks nœud] [ az-aks-nodepool-scale] commande. L’exemple suivant augmente le nombre de nœuds dans *mynodepool* à *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Répertorie l’état de vos pools de nœud à l’aide de la [liste du pool de nœud az aks] [ az-aks-nodepool-list] commande. L’exemple suivant montre que *mynodepool* est dans le *mise à l’échelle* état avec un nouveau décompte de *5* nœuds :

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Il prend quelques minutes avant que l’opération de mise à l’échelle se termine.

## <a name="delete-a-node-pool"></a>Supprimer un pool de nœud

Si vous n’avez plus besoin un pool, vous pouvez le supprimer et supprimer les nœuds de machine virtuelle sous-jacente. Pour supprimer un pool de nœuds, utilisez la [de suppression de pool de nœud az aks] [ az-aks-nodepool-delete] commande et spécifiez le nom de pool de nœud. L’exemple suivant supprime le *mynoodepool* créé aux étapes précédentes :

> [!CAUTION]
> Il n’existe aucune option de récupération pour la perte de données qui peuvent se produire lorsque vous supprimez un pool de nœuds. Si d’autres pools de nœuds ne peuvent pas être planifiées pods, ces applications ne sont pas disponibles. Veillez à que ne pas supprimer un pool de nœuds lorsque vous n’ont pas les applications en cours d’utilisation des sauvegardes de données ou la possibilité de s’exécuter sur d’autres pools de nœuds dans votre cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L’exemple de sortie suivant à partir de la [liste du pool de nœud az aks] [ az-aks-nodepool-list] commande montre que *mynodepool* est dans le *suppression* état :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Il prend quelques minutes pour supprimer les nœuds et le pool de nœud.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Spécifiez une taille de machine virtuelle pour un pool de nœuds

Dans les exemples précédents pour créer un pool de nœud, une taille de machine virtuelle par défaut a été utilisée pour les nœuds créés dans le cluster. Un scénario plus courant est de créer des pools de nœuds avec des fonctionnalités et les différentes tailles de machine virtuelle. Par exemple, vous pouvez créer un pool de nœud qui contient les nœuds de grandes quantités de mémoire ou de processeur ou un pool de nœuds qui fournit la prise en charge GPU. Dans l’étape suivante, vous [utilisation de soleil et tolerations][#schedule-pods-using-taints-and-tolerations] pour indiquer le planificateur Kubernetes comment limiter l’accès aux blocs pouvant s’exécuter sur ces nœuds.

Dans l’exemple suivant, créez un pool de nœud basées sur GPU qui utilise le *Standard_NC6* taille de machine virtuelle. Ces machines virtuelles sont alimentées par la carte NVIDIA Tesla K80. Pour plus d’informations sur les tailles de machine virtuelle disponibles, consultez [tailles des machines virtuelles Linux dans Azure][vm-sizes].

Créer un pool de nœud à l’aide de la [ajouter des nœuds du pool de az aks] [ az-aks-nodepool-add] réexécutez la commande. Cette fois, spécifiez le nom *gpunodepool*et utiliser le `--node-vm-size` paramètre pour spécifier le *Standard_NC6* taille :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L’exemple de sortie suivant à partir de la [liste du pool de nœud az aks] [ az-aks-nodepool-list] commande montre que *gpunodepool* est *création* nœuds avec le spécifié *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Il prend quelques minutes le *gpunodepool* doit être créé avec succès.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Planifier des pods à l’aide de soleil et tolerations

Vous avez maintenant deux pools de nœuds dans votre cluster, le pool de nœud par défaut initialement créé et le pool de nœud basées sur GPU. Utilisez le [kubectl obtenir des nœuds] [ kubectl-get] commande pour afficher les nœuds dans votre cluster. L’exemple de sortie suivant montre un seul nœud dans chaque pool de nœud :

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Le planificateur Kubernetes peut utiliser des teintes et des tolérances pour restreindre les charges de travail qui peuvent s’exécuter sur des nœuds.

* Quand une **teinte** est appliquée à un nœud, seuls des pods spécifiques peuvent être planifiés sur le nœud.
* Une **tolérance** est ensuite appliquée à un pod pour lui permettre de *tolérer* la teinte d’un nœud.

Pour plus d’informations sur l’utilisation avancée sur les fonctionnalités de Kubernetes planifiée, consultez [meilleures pratiques pour les fonctionnalités avancées du planificateur dans ACS][taints-tolerations]

Dans cet exemple, appliquer un goût à votre nœud basées sur GPU à l’aide de la [kubectl goût nœud] [ kubectl-taint] commande. Spécifiez le nom de votre nœud basées sur GPU à partir de la sortie de la précédente `kubectl get nodes` commande. Le goût est appliquée comme un *clé : valeur* et puis une option de planification. L’exemple suivant utilise le *référence (SKU) = gpu* coupler et définit les pods possède-t-elle la *NoSchedule* possibilité :

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

L’exemple de base YAML de manifeste suivant utilise un COMPLEMENTS pour autoriser le planificateur Kubernetes exécuter un bloc NGINX sur le nœud basées sur GPU. Pour obtenir un exemple plus approprié, mais beaucoup de temps exécuter une tâche Tensorflow sur le jeu de données MNIST, consultez [GPU d’utilisation pour les charges de travail de calcul intensif sur AKS][gpu-cluster].

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

Planifier le pod à l’aide de la `kubectl apply -f gpu-toleration.yaml` commande :

```console
kubectl apply -f gpu-toleration.yaml
```

Il faut quelques secondes pour planifier le pod et extrayez l’image NGINX. Utilisez le [kubectl décrivent pod] [ kubectl-describe] commande pour afficher l’état de pod. L’exemple condensé suivant sortie affiche le *référence (SKU) = gpu:NoSchedule* ALTERES est appliqué. Dans la section événements, le planificateur a affecté le pod à le *aks-gpunodepool-28993262-vmss000000* nœud basées sur GPU :

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

Uniquement les blocs qui ont ce goût appliqué peuvent être planifiées sur les nœuds dans *gpunodepool*. N’importe quel autre pod sera planifiée dans le *nodepool1* pool de nœud. Si vous créez des pools de nœuds supplémentaires, vous pouvez utiliser de soleil supplémentaires et tolerations pour limiter les pods peuvent être planifiées sur ces ressources de nœud.

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans cet article, vous avez créé un cluster AKS qui inclut des nœuds basés sur le GPU. Pour réduire les coûts inutiles, vous voudrez peut-être supprimer le *gpunodepool*, ou l’ensemble du cluster AKS.

Pour supprimer le pool de nœud basées sur GPU, utilisez le [supprimer az aks nodepool] [ az-aks-nodepool-delete] commande comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Pour supprimer le cluster lui-même, utilisez la [suppression du groupe az] [ az-group-delete] commande pour supprimer le groupe de ressources AKS :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer et gérer plusieurs pools de nœuds dans un cluster AKS. Pour plus d’informations sur la façon de contrôler les pods entre les pools de nœuds, consultez [meilleures pratiques pour les fonctionnalités avancées du planificateur dans ACS][operator-best-practices-advanced-scheduler].

Pour créer et utiliser des pools de nœuds de conteneur Windows Server, consultez [créer un conteneur Windows Server dans ACS][aks-windows].

<!-- EXTERNAL LINKS -->
[aks-github]: https://github.com/azure/aks/issues
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
