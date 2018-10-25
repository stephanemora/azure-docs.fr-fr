---
title: GPU sur Azure Kubernetes Service (AKS)
description: Utiliser les GPU sur Azure Kubernetes Service (AKS)
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: laevenso
ms.custom: mvc
ms.openlocfilehash: 231d7b875a7163aaa532be4a6477ca4e2eb67286
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043564"
---
# <a name="using-gpus-on-aks"></a>Utiliser le GPU sur AKS

AKS prend en charge la création de pools de nœuds avec processeur graphique (GPU). Azure fournit actuellement une ou plusieurs machines virtuelles avec processeur graphique (GPU). Les machines virtuelles avec processeur graphique (GPU) sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Une liste de ces machines virtuelles est disponible [ici](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Les GPU sont en général nécessaires pour les charges de travail intensives en calcul (traitement graphique et affichage). Consultez ce [document](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) pour déterminer la taille de machine virtuelle adaptée à votre charge de travail.
Nous recommandons une taille minimale de `Standard_NC6` pour vos nœuds Azure Kubernetes Service (AKS).

> [!NOTE]
> Les machines virtuelles avec processeur graphique (GPU) contiennent du matériel spécialisé, plus cher et soumis à une plus grande disponibilité dans différentes régions. Pour plus d’informations, consultez l’outil de [tarification](https://azure.microsoft.com/pricing/) et le site de [disponibilité dans les régions](https://azure.microsoft.com/global-infrastructure/services/).


Si vous avez besoin d’un cluster AKS qui respecte cette recommandation minimale, exécutez les commandes suivantes.

Créez un groupe de ressources pour le cluster.

```azurecli
az group create --name myGPUCluster --location eastus
```

Créez le cluster AKS avec des nœuds dont la taille est `Standard_NC6`.

```azurecli
az aks create --resource-group myGPUCluster --name myGPUCluster --node-vm-size Standard_NC6
```

Connectez-vous au cluster AKS.

```azurecli
az aks get-credentials --resource-group myGPUCluster --name myGPUCluster
```

## <a name="confirm-gpus-are-schedulable"></a>Vérifier que les GPU sont planifiables

Exécutez les commandes suivantes pour confirmer que les GPU sont planifiables via Kubernetes.

Récupérez la liste actuelle des nœuds.

```
$ kubectl get nodes
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-22139053-0   Ready     agent     10h       v1.9.6
aks-nodepool1-22139053-1   Ready     agent     10h       v1.9.6
aks-nodepool1-22139053-2   Ready     agent     10h       v1.9.6
```

Décrivez l’un des nœuds pour confirmer que les GPU sont planifiables. Ces informations sont disponibles dans la section `Capacity`. Par exemple : `nvidia.com/gpu:  1`. Si vous ne voyez pas les GPU, consultez la section de **dépannage** ci-dessous.

```
$ kubectl describe node aks-nodepool1-22139053-0
Name:               aks-nodepool1-22139053-0
Roles:              agent
Labels:             agentpool=nodepool1
                    beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/instance-type=Standard_NC6
                    beta.kubernetes.io/os=linux
                    failure-domain.beta.kubernetes.io/region=eastus
                    failure-domain.beta.kubernetes.io/zone=1
                    kubernetes.azure.com/cluster=MC_myGPUCluster_myGPUCluster
                    kubernetes.io/hostname=aks-nodepool1-22139053-0
                    kubernetes.io/role=agent
                    storageprofile=managed
                    storagetier=Standard_LRS
Annotations:        node.alpha.kubernetes.io/ttl=0
                    volumes.kubernetes.io/controller-managed-attach-detach=true
Taints:             <none>
CreationTimestamp:  Thu, 05 Apr 2018 12:13:20 -0700
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Thu, 05 Apr 2018 12:15:07 -0700   Thu, 05 Apr 2018 12:15:07 -0700   RouteCreated                 RouteController created a route
  OutOfDisk            False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasSufficientDisk     kubelet has sufficient disk space available
  MemoryPressure       False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasNoDiskPressure     kubelet has no disk pressure
  Ready                True    Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:15:10 -0700   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  10.240.0.4
  Hostname:    aks-nodepool1-22139053-0
Capacity:
 nvidia.com/gpu:                  1
 cpu:                             6
 memory:                          57691688Ki
 pods:                            110
Allocatable:
 nvidia.com/gpu:                  1
 cpu:                             6
 memory:                          57589288Ki
 pods:                            110
System Info:
 Machine ID:                 2eb0e90bd1fe450ba3cf83479443a511
 System UUID:                CFB485B6-CB49-A545-A2C9-8E4C592C3273
 Boot ID:                    fea24544-596d-4246-b8c3-610fc7ac7280
 Kernel Version:             4.13.0-1011-azure
 OS Image:                   Debian GNU/Linux 9 (stretch)
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.9.6
 Kube-Proxy Version:         v1.9.6
PodCIDR:                     10.244.1.0/24
ExternalID:                  /subscriptions/8ecadfc9-d1a3-4ea4-b844-0d9f87e4d7c8/resourceGroups/MC_myGPUCluster_myGPUCluster/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-22139053-0
Non-terminated Pods:         (2 in total)
  Namespace                  Name                       CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                       ------------  ----------  ---------------  -------------
  kube-system                kube-proxy-pwffr           100m (1%)     0 (0%)      0 (0%)           0 (0%)
  kube-system                kube-svc-redirect-mkpf4    0 (0%)        0 (0%)      0 (0%)           0 (0%)
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ------------  ----------  ---------------  -------------
  100m (1%)     0 (0%)      0 (0%)           0 (0%)
Events:         <none>
```

## <a name="run-a-gpu-enabled-workload"></a>Exécuter une charge de travail avec processeur graphique (GPU)

Pour démontrer que les GPU fonctionnent, planifiez une charge de travail avec processeur graphique à l’aide de la requête de ressource appropriée. Cet exemple exécute un travail [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) sur le [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/).

Le manifeste de travail suivant inclut une limite de ressource de `nvidia.com/gpu: 1`. 

Copiez le manifeste et enregistrez-le sous **samples-tf-mnist-demo.yaml**.
```
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

Utilisez la commande [kubectl apply][kubectl-apply] pour exécuter le travail. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.
```
$ kubectl apply -f samples-tf-mnist-demo.yaml
job "samples-tf-mnist-demo" created
```

Surveillez la progression du travail jusqu’à son achèvement complet à l’aide de la commande [kubectl get jobs] [ kubectl-get] et de l’argument `--watch`.
```
$ kubectl get jobs samples-tf-mnist-demo --watch
NAME                    DESIRED   SUCCESSFUL   AGE
samples-tf-mnist-demo   1         0            8s
samples-tf-mnist-demo   1         1            35s
```

Déterminez le nom de pod pour consulter les journaux en affichant les pods complets.
```
$ kubectl get pods --selector app=samples-tf-mnist-demo --show-all
NAME                          READY     STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1       Completed   0          4m
```

En utilisant le nom du pod indiqué dans la sortie de la commande ci-dessus, consultez les journaux du pod pour vérifier que l’appareil GPU approprié a été détecté, en l’occurrence `Tesla K80`.
```
$ kubectl logs samples-tf-mnist-demo-smnr6
2018-04-13 04:11:08.710863: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-04-13 04:11:15.824349: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 04e1:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2018-04-13 04:11:15.824394: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 04e1:00:00.0, compute capability: 3.7)
2018-04-13 04:11:20.891910: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.0487
Accuracy at step 10: 0.6571
Accuracy at step 20: 0.8111
Accuracy at step 30: 0.8562
Accuracy at step 40: 0.8786
Accuracy at step 50: 0.8911
Accuracy at step 60: 0.8986
Accuracy at step 70: 0.9017
Accuracy at step 80: 0.9049
Accuracy at step 90: 0.9114
Adding run metadata for 99
Accuracy at step 100: 0.9109
Accuracy at step 110: 0.9143
Accuracy at step 120: 0.9188
Accuracy at step 130: 0.9194
Accuracy at step 140: 0.9237
Accuracy at step 150: 0.9231
Accuracy at step 160: 0.9158
Accuracy at step 170: 0.9259
Accuracy at step 180: 0.9303
Accuracy at step 190: 0.9315
Adding run metadata for 199
Accuracy at step 200: 0.9334
Accuracy at step 210: 0.9342
Accuracy at step 220: 0.9359
Accuracy at step 230: 0.9353
Accuracy at step 240: 0.933
Accuracy at step 250: 0.9353
Accuracy at step 260: 0.9408
Accuracy at step 270: 0.9396
Accuracy at step 280: 0.9406
Accuracy at step 290: 0.9444
Adding run metadata for 299
Accuracy at step 300: 0.9453
Accuracy at step 310: 0.946
Accuracy at step 320: 0.9464
Accuracy at step 330: 0.9472
Accuracy at step 340: 0.9516
Accuracy at step 350: 0.9473
Accuracy at step 360: 0.9502
Accuracy at step 370: 0.9483
Accuracy at step 380: 0.9481
Accuracy at step 390: 0.9467
Adding run metadata for 399
Accuracy at step 400: 0.9477
Accuracy at step 410: 0.948
Accuracy at step 420: 0.9496
Accuracy at step 430: 0.9501
Accuracy at step 440: 0.9534
Accuracy at step 450: 0.9551
Accuracy at step 460: 0.9518
Accuracy at step 470: 0.9562
Accuracy at step 480: 0.9583
Accuracy at step 490: 0.9575
Adding run metadata for 499
```

## <a name="cleanup"></a>Nettoyage
Supprimez les objets Kubernetes associés, créés à cette étape.
```
$ kubectl delete jobs samples-tf-mnist-demo
job "samples-tf-mnist-demo" deleted
```

## <a name="troubleshoot"></a>Résolution des problèmes

Dans certains scénarios, les ressources GPU peuvent ne pas s’afficher sous Capacity. Par exemple, après la mise à niveau d’un cluster vers Kubernetes version 1.10 ou la création d’un cluster Kubernetes version 1.10, la ressource `nvidia.com/gpu` attendue ne figure pas dans `Capacity` quand vous exécutez `kubectl describe node <node-name>`. 

Pour résoudre ce problème, appliquez le daemonset suivant après le provisionnement ou la mise à niveau. `nvidia.com/gpu` doit ensuite apparaître comme une ressource planifiable. 

Copiez le manifeste et enregistrez-le sous **nvidia-device-plugin-ds.yaml**. Pour la balise d’image de `image: nvidia/k8s-device-plugin:1.10` ci-dessous, mettez à jour la balise pour qu’elle corresponde à votre version de Kubernetes. Par exemple, utilisez la balise `1.11` pour Kubernetes version 1.11.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: kube-system
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
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
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

Utilisez la commande [kubectl apply][kubectl-apply] pour créer le daemonset.

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml
daemonset "nvidia-device-plugin" created
```

## <a name="next-steps"></a>Étapes suivantes

Intéressé par l’exécution de charges de travail Machine Learning sur Kubernetes ? Pour plus d’informations, consultez Kubeflow Labs.

> [!div class="nextstepaction"]
> [Kubeflow Labs][kubeflow-labs]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
