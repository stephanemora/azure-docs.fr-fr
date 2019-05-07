---
title: Utiliser les GPU sur Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser des GPU pour le calcul haute performance ou les charges de travail nécessitant beaucoup de ressources graphiques sur Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 150eaa6a4df558ed0c737d99cbcc8010baf63e96
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073919"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Utiliser des GPU pour les charges de travail nécessitant beaucoup de ressources système sur Azure Kubernetes Service (AKS)

Les processeurs graphiques (GPU) sont souvent utilisés pour les charges de travail nécessitant beaucoup de ressources système, comme le traitement graphique et la visualisation. AKS prend en charge la création de pools de nœuds compatibles GPU pour exécuter ce type de charges de travail dans Kubernetes. Pour plus d’informations sur les machines virtuelles compatibles GPU disponibles, voir [Tailles de machines virtuelles optimisées pour les GPU dans Azure][gpu-skus]. Pour les nœuds AKS, nous recommandons la taille minimale *Standard_NC6*.

> [!NOTE]
> Les machines virtuelles compatibles GPU contiennent du matériel spécialisé, plus cher et dépendant de la disponibilité régionale. Pour plus d’informations, voir l’outil de [tarification][azure-pricing] et la [disponibilité régionale][azure-availability].

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez déjà d’un cluster AKS qui comporte des nœuds prenant en charge les GPU. Votre cluster AKS doit exécuter Kubernetes 1.10 ou une version ultérieure. Si vous avez besoin d’un cluster AKS qui réponde à ces exigences, consultez la première section de cet article, [Créer un cluster AKS](#create-an-aks-cluster).

Vous également besoin d’Azure CLI version 2.0.59 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Si vous avez besoin d’un cluster AKS qui remplisse les exigences minimales (nœud compatible GPU et Kubernetes 1.10 ou version ultérieure), suivez les étapes ci-dessous. Si vous disposez déjà d’un cluster AKS qui répond à ces exigences, [passer à la section suivante](#confirm-that-gpus-are-schedulable).

Tout d’abord, créez un groupe de ressources pour le cluster avec la commande [az group create][az-group-create]. L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* dans la région *USA Est* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez maintenant un cluster AKS avec la commande [az aks create][az-aks-create]. L’exemple suivant crée un cluster avec un seul nœud de taille `Standard_NC6`, et s’exécute Kubernetes version 1.11.7 :

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.8
```

Récupérez les informations d’identification de votre cluster AKS avec la commande [az aks get-credentials][az-aks-get-credentials] :

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Vérifier que les GPU sont planifiables

Maintenant que votre cluster AKS est créé, vérifiez que les GPU sont planifiables dans Kubernetes. Tout d’abord, listez les nœuds de votre cluster avec la commande [kubectl get nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

Utilisez maintenant la commande [kubectl describe node][kubectl-describe] pour vérifier que les GPU sont planifiables. Sous la section *Capacité*, le GPU doit apparaître ainsi : `nvidia.com/gpu:  1`. S’ils n’apparaissent pas, consultez la section [Résoudre les problèmes de disponibilité des GPU](#troubleshoot-gpu-availability).

L’exemple condensé suivant montre qu’un GPU est disponible sur le nœud nommé *aks-nodepool1-18821093-0* :

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Exécuter une charge de travail compatible GPU

Pour voir fonctionner le GPU, planifiez une charge de travail compatible GPU avec la demande de ressource correspondante. Cet exemple exécute une tâche [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) sur le [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/).

Créez un fichier nommé *samples-tf-mnist-demo.yaml* et collez le manifeste YAML suivant. Le manifeste de travail suivant comporte la limite de ressource `nvidia.com/gpu: 1` :

> [!NOTE]
> Si vous recevez une erreur d’incompatibilité de version lorsque vous appelez des pilotes (par exemple, la version du pilote CUDA est insuffisante pour la version du runtime CUDA), consultez la matrice de compatibilité des pilotes nVidia à l’adresse [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html).

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
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Utilisez la commande [kubectl apply][kubectl-apply] pour exécuter le travail. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis :

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Afficher l’état et la sortie de la charge de travail compatible GPU

Surveillez la progression de la tâche avec la commande [kubectl get jobs][kubectl-get] et l’argument `--watch`. L’extraction de l’image, puis le traitement du jeu de données peuvent prendre quelques minutes. Lorsque la colonne *COMPLETIONS* affiche *1/1*, la tâche est terminée :

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Pour voir la sortie de la charge de travail compatible GPU, commencez par récupérer le nom des pods avec la commande [kubectl get pods][kubectl-get] :

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Utilisez maintenant la commande [kubectl logs][kubectl-logs] pour afficher les journaux d’activité de pods. Les journaux d’activité de pods de l’exemple suivant confirment que l’appareil GPU détecté est le bon (`Tesla K80`). Indiquez le nom de votre propre pod :

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer les objets Kubernetes associés créés dans cet article, utilisez la commande [delete job][kubectl delete] de la manière suivante :

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Résoudre les problèmes de disponibilité des GPU

Si vous ne voyez pas de GPU disponibles sur vos nœuds, il vous faudra peut-être déployer un DaemonSet pour le plug-in d’appareil nVidia. Ce DaemonSet exécute un pod sur chaque nœud de façon à fournir les pilotes requis pour les GPU.

Commencez par créer un espace de noms avec la commande [kubectl create namespace][kubectl-create], par exemple, *gpu-ressources* :

```console
kubectl create namespace gpu-resources
```

Créez un fichier nommé *nvidia-device-plugin-ds.yaml* et collez le manifeste YAML suivant. Mettez à jour `image: nvidia/k8s-device-plugin:1.11` au milieu du manifeste en fonction de votre version de Kubernetes. Par exemple, si votre cluster AKS s’exécute Kubernetes version 1.12, mettre à jour la balise à `image: nvidia/k8s-device-plugin:1.12`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
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
      containers:
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
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
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Utilisez maintenant la commande [kubectl apply][kubectl-apply] pour créer le DaemonSet :

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Exécutez à nouveau la commande [kubectl describe node][kubectl-describe] pour vérifier que le GPU est à présent disponible sur le nœud.

## <a name="next-steps"></a>Étapes suivantes

Pour exécuter des tâches Apache Spark, voir [Exécuter des tâches Apache Spark sur AKS][aks-spark].

Pour plus d’informations sur les charges de travail Machine Learning (ML) sur Kubernetes, voir [Kubeflow Labs][kubeflow-labs].

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

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
