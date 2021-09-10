---
title: Utiliser les GPU sur Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser des GPU pour le calcul haute performance ou les charges de travail nécessitant beaucoup de ressources graphiques sur Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: fa7415f015ad17cc2e8a5ff4822c8ff53578f054
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525325"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Utiliser des GPU pour les charges de travail nécessitant beaucoup de ressources système sur Azure Kubernetes Service (AKS)

Les processeurs graphiques (GPU) sont souvent utilisés pour les charges de travail nécessitant beaucoup de ressources système, comme le traitement graphique et la visualisation. AKS prend en charge la création de pools de nœuds compatibles GPU pour exécuter ce type de charges de travail dans Kubernetes. Pour plus d’informations sur les machines virtuelles compatibles GPU disponibles, voir [Tailles de machines virtuelles optimisées pour les GPU dans Azure][gpu-skus]. Pour les pools de nœuds AKS, nous recommandons la taille minimale *Standard_NC6*.

> [!NOTE]
> Les machines virtuelles compatibles GPU contiennent du matériel spécialisé, plus cher et dépendant de la disponibilité régionale. Pour plus d’informations, voir l’outil de [tarification][azure-pricing] et la [disponibilité régionale][azure-availability].

Actuellement, vous ne pouvez utiliser de pools de nœuds compatibles GPU que sous Linux.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez [Démarrage rapide : Déployer un cluster Azure Kubernetes Service à l’aide d’Azure CLI][aks-quickstart].

Le logiciel Azure CLI version 2.0.64 ou ultérieure doit également être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="get-the-credentials-for-your-cluster"></a>Obtenir les informations d’identification de votre cluster

Récupérez les informations d’identification de votre cluster AKS avec la commande [az aks get-credentials][az-aks-get-credentials]. L’exemple de commande suivant récupère les informations d’identification du cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup*.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-the-nvidia-device-plugin"></a>Ajouter le plug-in d’appareil NVIDIA

Il existe deux options pour ajouter le plug-in d’appareil NVIDIA :

* Utiliser l’image de GPU AKS
* Installer manuellement le plug-in de périphérique NVIDIA

> [!WARNING]
> Vous pouvez utiliser l’une ou l’autre des options ci-dessus, mais vous ne devez pas installer manuellement le jeu de démons du plug-in d’appareil NVIDIA avec des clusters qui utilisent l’image de GPU AKS.

### <a name="update-your-cluster-to-use-the-aks-gpu-image-preview"></a>Mettre à jour votre cluster pour utiliser l’image de GPU AKS (préversion)

AKS fournit une image AKS entièrement configurée qui contient déjà le [plug-in d’appareil NVIDIA pour Kubernetes][nvidia-github].

Inscrivez la fonctionnalité `GPUDedicatedVHDPreview` :

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état **Inscrit** s’affiche. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list](/cli/azure/feature#az_feature_list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Quand l’état indique Inscrit, actualisez l’inscription du fournisseur de ressources `Microsoft.ContainerService` à l’aide de la commande [az provider register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Pour installer l’extension CLI aks-preview, utilisez les commandes Azure CLI suivantes :

```azurecli
az extension add --name aks-preview
```

Pour mettre à jour l’extension CLI aks-preview, utilisez les commandes Azure CLI suivantes :

```azurecli
az extension update --name aks-preview
```

## <a name="add-a-node-pool-for-gpu-nodes"></a>Ajouter un pool de nœuds pour les nœuds GPU

Pour ajouter un pool de nœuds à votre cluster, utilisez [az aks nodepool add][az-aks-nodepool-add].

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --aks-custom-headers UseGPUDedicatedVHD=true \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

La commande ci-dessus ajoute un pool de nœuds nommé *gpunp* au cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup*. La commande définit également la taille de machine virtuelle pour les nœuds du pool de nœuds sur *Standard_NC6*, active l’autoscaler de cluster, configure ce dernier pour maintenir au minimum un nœud et au maximum trois nœuds dans le pool de nœuds, spécifie des nœuds d’image de GPU AKS spécialisés sur votre nouveau pool de nœuds et spécifie une teinte *sku=gpu:NoSchedule* pour le pool de nœuds.

> [!NOTE]
> Une teinte et une taille de machine virtuelle ne peuvent être définies que pour les pools de nœuds lors de la création du pool de nœuds, mais les paramètres de mise à l’échelle automatique peuvent être mis à jour à tout moment.

> [!NOTE]
> Si la référence SKU de votre GPU requiert la génération de deux machines virtuelles, utilisez *--aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true*. Par exemple :
> 
> ```azurecli
> az aks nodepool add \
>    --resource-group myResourceGroup \
>    --cluster-name myAKSCluster \
>    --name gpunp \
>    --node-count 1 \
>    --node-vm-size Standard_NC6 \
>    --node-taints sku=gpu:NoSchedule \
>    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
>    --enable-cluster-autoscaler \
>    --min-count 1 \
>    --max-count 3
> ```

### <a name="manually-install-the-nvidia-device-plugin"></a>Installer manuellement le plug-in de périphérique NVIDIA

Vous pouvez également déployer un DaemonSet pour le plug-in d’appareil NVIDIA. Ce DaemonSet exécute un pod sur chaque nœud de façon à fournir les pilotes requis pour les GPU.

Ajoutez un pool de nœuds à votre cluster en utilisant [az aks nodepool add][az-aks-nodepool-add].

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

La commande ci-dessus ajoute un pool de nœuds nommé *gpunp* au cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup*. La commande définit également la taille de machine virtuelle pour les nœuds du pool de nœuds sur *Standard_NC6*, active l’autoscaler de cluster, configure ce dernier pour maintenir au minimum un nœud et au maximum trois nœuds dans le pool de nœuds et spécifie une teinte *sku=gpu:NoSchedule* pour le pool de nœuds.

> [!NOTE]
> Une teinte et une taille de machine virtuelle ne peuvent être définies que pour les pools de nœuds lors de la création du pool de nœuds, mais les paramètres de mise à l’échelle automatique peuvent être mis à jour à tout moment.

Créez un espace de noms avec la commande [kubectl create namespace][kubectl-create], par exemple, *gpu-ressources* :

```console
kubectl create namespace gpu-resources
```

Créez un fichier nommé *nvidia-device-plugin-ds.yaml* et collez le manifeste YAML suivant. Ce manifeste est fourni dans le cadre du [plug-in d’appareil NVIDIA pour un projet Kubernetes][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Utilisez [kubectl apply][kubectl-apply] pour créer un DaemonSet et confirmez la création du plug-in d’appareil NVIDIA, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Vérifier que les GPU sont planifiables

Maintenant que votre cluster AKS est créé, vérifiez que les GPU sont planifiables dans Kubernetes. Tout d’abord, listez les nœuds de votre cluster avec la commande [kubectl get nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                   STATUS   ROLES   AGE   VERSION
aks-gpunp-28993262-0   Ready    agent   13m   v1.20.7
```

Utilisez maintenant la commande [kubectl describe node][kubectl-describe] pour vérifier que les GPU sont planifiables. Sous la section *Capacité*, le GPU doit apparaître ainsi : `nvidia.com/gpu:  1`.

L’exemple condensé suivant montre qu’un GPU est disponible sur le nœud nommé *aks-nodepool1-18821093-0* :

```console
$ kubectl describe node aks-gpunp-28993262-0

Name:               aks-gpunp-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
[...]
 nvidia.com/gpu:                 1
[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Exécuter une charge de travail compatible GPU

Pour voir fonctionner le GPU, planifiez une charge de travail compatible GPU avec la demande de ressource correspondante. Cet exemple exécute une tâche [Tensorflow](https://www.tensorflow.org/) sur le [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/).

Créez un fichier nommé *samples-tf-mnist-demo.yaml* et collez le manifeste YAML suivant. Le manifeste de travail suivant comporte la limite de ressource `nvidia.com/gpu: 1` :

> [!NOTE]
> Si vous recevez une erreur d’incompatibilité de version lorsque vous appelez des pilotes (par exemple, la version du pilote CUDA est insuffisante pour la version du runtime CUDA), consultez la matrice de compatibilité des pilotes NVIDIA à l’adresse [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
      tolerations:
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
```

Utilisez la commande [kubectl apply][kubectl-apply] pour exécuter le travail. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis :

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Afficher l’état et la sortie de la charge de travail compatible GPU

Surveillez la progression de la tâche avec la commande [kubectl get jobs][kubectl-get] et l’argument `--watch`. L’extraction de l’image, puis le traitement du jeu de données peuvent prendre quelques minutes. Lorsque la colonne *COMPLETIONS* affiche *1/1*, la tâche est terminée. Quitter la commande `kubetctl --watch` avec *Ctrl-C* :

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Pour voir la sortie de la charge de travail compatible GPU, commencez par récupérer le nom des pods avec la commande [kubectl get pods][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Utilisez maintenant la commande [kubectl logs][kubectl-logs] pour afficher les journaux d’activité de pods. Les journaux d’activité de pods de l’exemple suivant confirment que l’appareil GPU détecté est le bon (`Tesla K80`). Indiquez le nom de votre propre pod :

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="use-container-insights-to-monitor-gpu-usage"></a>Utiliser Container Insights pour surveiller l’utilisation du GPU

Les métriques suivantes sont disponibles pour [Container Insights avec AKS][aks-container-insights] pour surveiller l’utilisation du GPU.

| Nom de métrique | Dimension de la métrique (balises) | Description |
|-------------|-------------------------|-------------|
| containerGpuDutyCycle | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor`   | Pourcentage de temps durant lequel le GPU est resté occupé/en cours de traitement pour un conteneur au cours de la période d’échantillonnage passée (60 secondes). Le cycle d’utilisation est un nombre compris entre 1 et 100. |
| containerGpuLimits | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | Chaque conteneur peut spécifier des limites pour un ou plusieurs GPU. Il n’est pas possible de demander ou de limiter une partie d’un GPU. |
| containerGpuRequests | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | Chaque conteneur peut demander un ou plusieurs GPU. Il n’est pas possible de demander ou de limiter une partie d’un GPU. |
| containerGpumemoryTotalBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Quantité de mémoire de GPU en octets à disposition pour un conteneur spécifique. |
| containerGpumemoryUsedBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Quantité de mémoire de GPU en octets utilisée par un conteneur spécifique. |
| nodeGpuAllocatable | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Nombre de GPU dans un nœud pouvant être utilisés par Kubernetes. |
| nodeGpuCapacity | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Nombre total de GPU présents dans un nœud. |

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les objets Kubernetes associés créés dans cet article, utilisez la commande [delete job][kubectl delete] de la manière suivante :

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Étapes suivantes

Pour exécuter des tâches Apache Spark, voir [Exécuter des tâches Apache Spark sur AKS][aks-spark].

Pour plus d’informations sur les charges de travail Machine Learning (ML) sur Kubernetes, voir [Kubeflow Labs][kubeflow-labs].

Pour plus d'informations sur l'utilisation d'Azure Kubernetes Service avec Azure Machine Learning, consultez les articles suivants :

* [Déployer un modèle sur Azure Kubernetes Service][azureml-aks]
* [Déployer un modèle de Deep Learning pour une inférence avec GPU][azureml-gpu]
* [Haute performance avec un serveur d'inférence Triton][azureml-triton]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
[azureml-aks]: ../machine-learning/how-to-deploy-azure-kubernetes-service.md
[azureml-gpu]: ../machine-learning/how-to-deploy-inferencing-gpus.md
[azureml-triton]: ../machine-learning/how-to-deploy-with-triton.md
[aks-container-insights]: monitor-aks.md#container-insights