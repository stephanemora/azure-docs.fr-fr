---
title: Déployer la charge de travail Kubernetes à l’aide du partage GPU sur un appareil GPU Azure Stack Edge Pro
description: Décrit comment vous pouvez déployer une charge de travail partagée GPU via Kubernetes sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 04299ba4028de313f640074ca98c0b611f734981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564616"
---
# <a name="deploy-a-kubernetes-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Déployer une charge de travail Kubernetes à l’aide du partage GPU sur votre appareil Azure Stack Edge Pro

Cet article décrit comment les charges de travail en conteneur peuvent partager les GPU sur votre appareil GPU Azure Stack Edge Pro. Dans cet article, vous allez exécuter deux travaux, l’un sans le partage de contexte GPU et l’autre avec le partage de contexte activé via Multi-Process Service (MPS) sur l’appareil. Pour en savoir plus, consultez la rubrique [Multi-Process Service](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez accès à un appareil GPU Azure Stack Edge Pro qui est [activé](azure-stack-edge-gpu-deploy-activate.md) et sur lequel est [configuré le calcul](azure-stack-edge-gpu-deploy-configure-compute.md). Vous avez le [point de terminaison de l’API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) et vous avez ajouté ce point de terminaison au fichier `hosts` sur votre client qui accédera à l’appareil.

1. Vous avez accès à un système client avec un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Si vous utilisez un client Windows, le système doit exécuter PowerShell 5.0 ou une version ultérieure pour accéder à l’appareil.

1. Vous avez créé un espace de noms et un utilisateur. Vous avez également accordé à l’utilisateur l’accès à cet espace de noms. Vous avez le fichier kubeconfig de cet espace de noms installé sur le système client que vous allez utiliser pour accéder à votre appareil. Pour connaître les instructions détaillées, consultez [Se connecter à un cluster Kubernetes et le gérer par le biais de kubectl sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac). 

1. Enregistrez le fichier `yaml` de déploiement suivant sur votre système local. Vous utiliserez ce fichier pour exécuter le déploiement Kubernetes. Ce déploiement est basé sur des [conteneurs CUDA simples](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) qui sont disponibles publiquement auprès de NVIDIA. 

    ```yml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample1
    spec:
      template:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container1
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ---
    
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample2
    spec:
      template:
        metadata:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container2
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Vérifier le pilote GPU, version CUDA

La première étape consiste à vérifier que votre appareil exécute les versions de pilote GPU et CUDA requises.

1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Exécutez la commande suivante :

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```

1. Dans la sortie SMI NVIDIA, prenez note de la version du GPU et de la version de CUDA sur votre appareil. Si vous exécutez le logiciel Azure Stack Edge 2102, cette version correspond aux versions de pilote suivantes :

    - Version du pilote GPU : 460.32.03
    - Version CUDA : 11.2
    
    Voici un exemple de sortie :

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:24:27 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS> 
    ```

1. Conservez cette session ouverte, car vous allez l’utiliser pour afficher la sortie SMI NVIDIA tout au long de l’article.



## <a name="job-without-context-sharing"></a>Travail sans partage de contexte

Vous allez exécuter la première tâche pour déployer une application sur votre appareil dans l’espace de noms `mynamesp1`. Ce déploiement d’application indique également que le partage de contexte GPU n’est pas activé par défaut. 

1. Dressez la liste de tous les pods exécutés dans l’espace de noms. Exécutez la commande suivante : 

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    ```
1. Démarrez un travail de déploiement sur votre appareil en utilisant le fichier deployment.yaml fourni précédemment. Exécutez la commande suivante : 

    ```powershell
    kubectl apply -f <Path to the deployment .yaml> -n <Name of the namespace> 
    ```  

    Ce travail crée deux conteneurs et exécute une simulation n-body sur les deux conteneurs. Le nombre d’itérations de simulation est spécifié dans le fichier `.yaml`. Pour plus d’informations, consultez [simulation n-body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).
    
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl apply -f -n mynamesp1 C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32>
    ```

1. Pour répertorier les pods démarrés dans le déploiement, exécutez la commande suivante :

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```   

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          28s
    cuda-sample2-db9vx   1/1     Running   0          27s
    PS C:\WINDOWS\system32>
    ```

    Deux pods, `cuda-sample1-cf979886d-xcwsq` et `cuda-sample2-68b4899948-vcv68`, s’exécutent sur votre appareil.

1. Récupérez les informations relatives aux pods. Exécutez la commande suivante :

    ```powershell
    kubectl -n <Name of the namespace> describe <Name of the job> 
    ```  

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
    Labels:         controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:34 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample1-27srm
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
    Labels:         controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:35 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample2-db9vx
    PS C:\WINDOWS\system32>
    ```
    La sortie indique que les deux pods ont été correctement créés par le travail. 

1. Tandis que les deux conteneurs exécutent la simulation n-body, consultez l’utilisation du GPU dans la sortie SMI NVIDIA. Accédez à l’interface PowerShell de l’appareil et exécutez `Get-HcsGpuNvidiaSmi`.

    Voici un exemple de sortie lorsque les deux conteneurs exécutent la simulation n-body :

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:26:41 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   64C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    197976      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    198051      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>    
    ```
    Comme vous pouvez le voir, deux conteneurs (type = C) s’exécutent avec la simulation n-body sur le GPU 0. 

1. Surveillez la simulation n-body. Exécutez les commandes `get pod`. Voici un exemple de sortie lorsque la simulation est en cours d’exécution. 

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          70s
    cuda-sample2-db9vx   1/1     Running   0          69s
    PS C:\WINDOWS\system32>
    ```

    Une fois la simulation terminée, la sortie indiquera cela. Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS      RESTARTS   AGE
    cuda-sample1-27srm   0/1     Completed   0          2m54s
    cuda-sample2-db9vx   0/1     Completed   0          2m53s
    PS C:\WINDOWS\system32>
    ```
 
1. Une fois la simulation terminée, vous pouvez consulter les journaux et la durée totale d’exécution de la simulation. Exécutez la commande suivante :

    ```powershell
    kubectl logs -n <Name of the namespace> <pod name>
    ``` 

    Voici un exemple de sortie : 

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-27srm
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================  
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170398.766 ms
    = 98.459 billion interactions per second
    = 1969.171 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>
    ```

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-db9vx
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170368.859 ms
    = 98.476 billion interactions per second
    = 1969.517 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>    
    ```
1. Aucun processus ne doit être en cours d’exécution sur le GPU. Pour le vérifier, consultez les données d’utilisation du GPU dans la sortie SMI NVIDIA.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:32:52 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   38C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```

## <a name="job-with-context-sharing"></a>Travail avec partage de contexte

Vous exécuterez la deuxième tâche pour déployer la simulation n-body sur deux conteneurs CUDA lorsque le partage de contexte GPU est activé à l’aide de MPS. Vous devez d’abord activer MPS sur l’appareil.

1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Pour activer MPS sur votre appareil, exécutez la commande `Start-HcsGpuMPS`.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    
    Set compute mode to EXCLUSIVE_PROCESS for GPU 00002C74:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Exécutez la tâche en utilisant le même fichier `yaml` de déploiement que celui utilisé précédemment. Vous devrez peut-être supprimer le déploiement existant. Consultez [Supprimer un déploiement](#delete-deployment).

    Voici un exemple de sortie :

    ```yml
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 delete -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch "cuda-sample1" deleted
    job.batch "cuda-sample2" deleted
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 apply -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-vcznt   1/1     Running   0          21s
    cuda-sample2-zkx4w   1/1     Running   0          21s
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
    Labels:         controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  46s   job-controller  Created pod: cuda-sample1-vcznt
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
    Labels:         controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  47s   job-controller  Created pod: cuda-sample2-zkx4w
    PS C:\WINDOWS\system32>
    ```

1. Pendant l’exécution de la simulation, vous pouvez afficher la sortie SMI NVIDIA. La sortie affiche les processus correspondant aux conteneurs CUDA (type M + C) avec la simulation n-body et le service MPS (type C) comme étant en cours d’exécution. Tous ces processus partagent le GPU 0.

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:54:50 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   45C    P0    68W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144377    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144379    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

1. Une fois la simulation terminée, vous pouvez consulter les journaux et la durée totale d’exécution de la simulation. Exécutez la commande suivante :

    ```powershell
        PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
        NAME                 READY   STATUS      RESTARTS   AGE
        cuda-sample1-vcznt   0/1     Completed   0          5m44s
        cuda-sample2-zkx4w   0/1     Completed   0          5m44s
        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-vcznt
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================    
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154979.453 ms
        = 108.254 billion interactions per second
        = 2165.089 single-precision GFLOP/s at 20 flops per interaction


        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-zkx4w
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154986.734 ms
        = 108.249 billion interactions per second
        = 2164.987 single-precision GFLOP/s at 20 flops per interaction
        PS C:\WINDOWS\system32>
    ```
1. Une fois la simulation terminée, vous pouvez afficher à nouveau la sortie SMI NVIDIA. Seul le processus nvidia-cuda-mps-server pour le service MPS s’affiche comme étant en cours d’exécution. 

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:59:55 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   37C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

## <a name="delete-deployment"></a>Supprimer un déploiement

Vous devrez peut-être supprimer les déploiements lors de l’exécution avec MPS activé et avec MPS désactivé sur votre appareil.

Pour supprimer le déploiement sur votre appareil, exécutez la commande suivante : 

```powershell
kubectl delete -f <Path to the deployment .yaml> -n <Name of the namespace> 
```

Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> kubectl delete -f 'C:\gpu-sharing\k8-gpusharing.yaml' -n mynamesp1
deployment.apps "cuda-sample1" deleted
deployment.apps "cuda-sample2" deleted
PS C:\WINDOWS\system32>
```
    
## <a name="next-steps"></a>Étapes suivantes

- [Déployer une charge de travail IOT Edge avec le partage GPU sur votre Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
