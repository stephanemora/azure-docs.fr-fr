---
title: Déployer une charge de travail IoT Edge à l’aide du partage GPU sur un appareil GPU Azure Stack Edge Pro
description: Décrit comment vous pouvez déployer une charge de travail partagée avec GPU via IoT Edge sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564618"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Déployer une charge de travail IoT Edge avec le partage GPU sur votre Azure Stack Edge Pro.

Cet article décrit comment les charges de travail en conteneur peuvent partager les GPU sur votre appareil GPU Azure Stack Edge Pro. L’approche implique l’activation de Multi-Process Service (MPS), puis la spécification des charges de travail GPU via un déploiement de IoT Edge. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez accès à un appareil GPU Azure Stack Edge Pro qui est [activé](azure-stack-edge-gpu-deploy-activate.md) et sur lequel est [configuré le calcul](azure-stack-edge-gpu-deploy-configure-compute.md). Vous avez le [point de terminaison de l’API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) et vous avez ajouté ce point de terminaison au fichier `hosts` sur votre client qui accédera à l’appareil.

1. Vous avez accès à un système client avec un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Si vous utilisez un client Windows, le système doit exécuter PowerShell 5.0 ou une version ultérieure pour accéder à l’appareil.

1. Enregistrez le fichier `json` de déploiement suivant sur votre système local. Vous utiliserez les informations de ce fichier pour exécuter le déploiement IoT Edge. Ce déploiement est basé sur des [conteneurs CUDA simples](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) qui sont disponibles publiquement auprès de NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Vérifier le pilote GPU, version CUDA

La première étape consiste à vérifier que votre appareil exécute les versions de pilote GPU et CUDA requises.

1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Exécutez la commande suivante :

    `Get-HcsGpuNvidiaSmi`

1. Dans la sortie SMI NVIDIA, prenez note de la version du GPU et de la version de CUDA sur votre appareil. Si vous exécutez le logiciel Azure Stack Edge 2102, cette version correspond aux versions de pilote suivantes :

    - Version du pilote GPU : 460.32.03
    - Version CUDA : 11.2
    
    Voici un exemple de sortie :

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
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


## <a name="deploy-without-context-sharing"></a>Déployer sans partage de contexte

Vous pouvez maintenant déployer une application sur votre appareil lorsque Multi-Process Service n’est pas en cours d’exécution et qu’il n’existe pas de partage de contexte. Le déploiement s’effectue via le portail Azure dans l’espace de noms `iotedge` qui existe sur votre appareil.

### <a name="create-user-in-iot-edge-namespace"></a>Créer un utilisateur dans l’espace de noms IoT Edge

Vous allez commencer par créer un utilisateur qui se connectera à l’espace de noms `iotedge`. Les modules IoT Edge sont déployés dans l’espace de noms iotedge. Pour plus d’informations, consultez la section [Espace de noms Kubernetes sur votre appareil](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Procédez comme suit pour créer un utilisateur et lui accorder l’accès à l’espace de noms `iotedge`. 

1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Créez un nouvel utilisateur dans l’espace de noms `iotedge`. Exécutez la commande suivante :

    `New-HcsKubernetesUser -UserName <user name>`

    Voici un exemple de sortie :

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Copiez la sortie affichée en texte brut. Enregistrez la sortie sous la forme d’un fichier de *configuration* (sans extension) dans le dossier `.kube` de votre profil utilisateur sur votre ordinateur local, par exemple `C:\Users\<username>\.kube`. 

1. Accordez à l’utilisateur que vous avez créé l’accès à l’espace de noms `iotedge`. Exécutez la commande suivante :

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Voici un exemple de sortie :

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Pour connaître les instructions détaillées, consultez [Se connecter à un cluster Kubernetes et le gérer par le biais de kubectl sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Déployer des modules via le portail

Déployer des modules IoT Edge à l’aide du portail Azure Vous allez déployer des exemples de modules NVIDIA CUDA disponibles au public qui exécutent la simulation n-body. Pour plus d’informations, consultez [Simulation n-body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Assurez-vous que le service IoT Edge est en cours d’exécution sur votre appareil.

    ![Service IoT Edge en cours d’exécution](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Dans le volet droit, sélectionnez la vignette IoT Edge. Accédez à **IoT Edge > Propriétés**. Dans le volet droit, sélectionnez la ressource IoT Hub associée à votre appareil.

    ![Afficher les propriétés.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. Dans la ressource IoT Hub, accédez à **Gestion automatique des appareils > IoT Edge**. Dans le volet droit, sélectionnez le service IoT Hub associée à votre appareil.

    ![Accédez à IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Sélectionnez **Définir des modules**.

    ![Accédez à Définir les modules.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Sélectionnez **+Ajouter > + Module IoT Edge**.

    ![Ajoutez un module IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Dans l’onglet **Paramètres du module**, indiquez le **nom du module IoT Edge** et l’**URI de l’image**. Définissez la **Stratégie d’extraction d’image** sur la valeur **Lors de la création**.

    ![Paramètres du module](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Dans l’onglet **Variables d’environnement**, définissez **NVIDIA_VISIBLE_DEVICES** sur **0**.

    ![Variables d'environnement.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Dans l’onglet **Options de création de conteneur**, indiquez les options suivantes :

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Les options s’affichent de la manière suivante :

    ![Options de création de conteneur](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Sélectionnez **Ajouter**.

1. Le module que vous avez ajouté doit indiquer l’état **En cours d’exécution**. 

    ![Examiner et créer un déploiement](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Répétez l’ensemble de la procédure d’ajout d’un module que vous avez suivie lors de l’ajout du premier module. Dans cet exemple, fournissez le nom du module sous la forme `cuda-sample2` . 

    ![Paramètres du module pour le 2e module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Utilisez la même variable d’environnement, car les deux modules partageront le même GPU.

    ![Variable d’environnement pour le 2e module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Utilisez les options de création de conteneur que vous avez fournies pour le premier module, puis sélectionnez **Ajouter**.

    ![Options de création de conteneur pour le 2e module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Dans la page **Définir les modules** , sélectionnez **Examiner + créer**, puis **Créer**. 

    ![Examiner et créer le 2e déploiement](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. L’**état d’exécution** des deux modules doit maintenant indiquer **En cours d’exécution**.  

    ![État du 2e déploiement](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Surveiller le déploiement de la charge de travail

1. Ouvrez une nouvelle session PowerShell.

1. Dressez la liste de tous les pods exécutés dans l’espace de noms `iotedge`. Exécutez la commande suivante :

    `kubectl get pods -n iotedge`   

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Deux pods, `cuda-sample1-97c494d7f-lnmns` et `cuda-sample2-d9f6c4688-2rld9`, s’exécutent sur votre appareil.

1. Tandis que les deux conteneurs exécutent la simulation n-body, consultez l’utilisation du GPU dans la sortie SMI NVIDIA. Accédez à l’interface PowerShell de l’appareil et exécutez `Get-HcsGpuNvidiaSmi`.

    Voici un exemple de sortie lorsque les deux conteneurs exécutent la simulation n-body :

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Comme vous pouvez le voir, deux conteneurs s’exécutent avec la simulation n-body sur le GPU 0. Vous pouvez également afficher l’utilisation de la mémoire correspondante.

1. Une fois la simulation terminée, la sortie SMI NVIDIA indique qu’aucun processus n’est en cours d’exécution sur l’appareil.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
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
 
1. Une fois la simulation n-body terminée, consultez les journaux pour comprendre les détails du déploiement et le temps nécessaire à l’exécution de la simulation. 

    Voici un exemple de sortie du premier conteneur :

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Voici un exemple de sortie du deuxième conteneur :

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Arrêtez le déploiement du module. Dans la ressource IoT Hub de votre appareil :
    1. Accédez à **Déploiement automatique des appareils > IoT Edge**. Sélectionnez l’appareil IoT Edge correspondant à votre appareil.

    1. Accédez à **Définir des modules** et sélectionnez un module. 

        ![Sélectionnez Définir le module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Dans l’onglet **Modules**, sélectionnez un module.
    
        ![Sélectionnez un module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Dans l’onglet **Paramètres du module**, définissez l’**état souhaité** sur Arrêté. Sélectionnez **Mettre à jour**.

        ![Modifier les paramètres du module](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Répétez les étapes pour arrêter le deuxième module déployé sur l’appareil. Sélectionnez **Vérifier + créer**, puis **Créer**. Le déploiement doit être mis à jour.

        ![Passez en revue et créez un déploiement mis à jour.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Actualisez plusieurs fois la page **Définir des modules**, jusqu’à ce que l’**État d’exécution** du module indique **Arrêté**.

        ![Vérifier l’état du déploiement](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Déployer avec partage de contexte

Vous pouvez maintenant déployer la simulation n-body sur deux conteneurs CUDA lorsque MPS s’exécute sur votre appareil. Vous devez d’abord activer MPS sur l’appareil.


1. [Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Pour activer MPS sur votre appareil, exécutez la commande `Start-HcsGpuMPS`.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Récupérez la sortie SMI NVIDIA à partir de l’interface PowerShell de l’appareil. Vous constatez que le processus `nvidia-cuda-mps-server` ou le service MPS s’exécute sur l’appareil.

    Voici un exemple de sortie :

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Déployez les modules que vous avez arrêtés précédemment. Définissez l’**État souhaité** sur En cours d’exécution via l’option **Définir des modules**.

    Voici l’exemple de sortie :

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Vous constatez que les modules sont déployés et en cours d’exécution sur votre appareil.

1. Lorsque les modules sont déployés, la simulation n-body commence également à s’exécuter sur les deux conteneurs. Voici l’exemple de sortie lorsque la simulation est terminée sur le premier conteneur :

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Voici l’exemple de sortie lorsque la simulation est terminée sur le deuxième conteneur :

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Récupérez la sortie SMI NVIDIA auprès de l’interface PowerShell de l’appareil lorsque les deux conteneurs exécutent la simulation n-body. Voici un exemple de sortie : Il y a trois processus, le processus `nvidia-cuda-mps-server` (type C) correspond au service MPS et les processus `/tmp/nbody` (type M + C) correspondent aux charges de travail de la simulation n-body déployées par les modules. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Étapes suivantes

- [Déployer une charge de travail Kubernetes avec GPU partagée sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)
