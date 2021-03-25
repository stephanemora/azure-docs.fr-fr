---
title: Se connecter à un appareil Microsoft Azure Stack Edge Pro GPU et le gérer via l'interface Windows PowerShell | Microsoft Docs
description: Explique comment se connecter à un appareil Azure Stack Edge Pro GPU et le gérer via l'interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562159"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Gérer un appareil Azure Stack Edge Pro avec GPU via Windows PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

La solution Azure Stack Edge Pro vous permet de traiter des données et de les envoyer à Azure via le réseau. Cet article décrit certaines des tâches de gestion et de configuration pour votre appareil Azure Stack Edge Pro. Vous pouvez utiliser le portail Azure, l'interface utilisateur locale ou l'interface Windows PowerShell pour gérer votre appareil.

Cet article se concentre sur la façon dont vous pouvez vous connecter à l’interface PowerShell de l’appareil et les tâches que vous pouvez effectuer à l’aide de cette interface. 


## <a name="connect-to-the-powershell-interface"></a>Connexion à l’interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Affichage des informations sur l’appareil
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Afficher les informations du pilote de GPU

Si le rôle de calcul est configuré sur votre appareil, vous pouvez également obtenir les informations du pilote du GPU via l’interface PowerShell. 

1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez `Get-HcsGpuNvidiaSmi` pour obtenir les informations du pilote du GPU pour votre appareil.

    L’exemple suivant montre comment utiliser cette cmdlet :

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Prenez note des informations du pilote à partir de l’exemple de sortie de cette applet de commande.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Activer un service multiprocessus (MPS)

Un service multiprocessus (MPS) sur les GPU Nvidia fournit un mécanisme dans lequel les GPU peuvent être partagés par plusieurs tâches, où chaque tâche est se voit attribuer un certain pourcentage des ressources du GPU. MPS est une fonctionnalité en préversion sur votre appareil Azure Stack Edge Pro avec GPU. Pour activer MPS sur votre appareil, procédez comme suit :

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Réinitialisation de votre appareil

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Récupération des journaux de calcul

Si le rôle de calcul est configuré sur votre appareil, vous pouvez également obtenir les journaux de calcul via l’interface PowerShell.

1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez `Get-AzureDataBoxEdgeComputeRoleLogs` pour obtenir les journaux de calcul de votre appareil.

    L’exemple suivant montre comment utiliser cette cmdlet :

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Voici une description des paramètres utilisés pour la cmdlet :
    - `Path`: Fournir un chemin d’accès réseau au partage dans lequel vous souhaitez créer le package de journaux de calcul.
    - `Credential`: Indiquez le nom d’utilisateur pour le partage réseau. Lorsque vous exécutez cette cmdlet, vous devez fournir le mot de passe du partage.
    - `FullLogCollection`: Ce paramètre garantit que le package de journaux contiendra tous les journaux de calcul. Par défaut, le package de journaux contient uniquement un sous-ensemble des journaux.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Modifier des sous-réseaux de pod et de service Kubernetes

Par défaut, Kubernetes sur votre appareil Azure Stack Edge utilise des sous-réseaux 172.27.0.0/16 et 172.28.0.0/16 respectivement pour le pod et le service. Si ces sous-réseaux sont déjà utilisés dans votre réseau, vous pouvez exécuter la cmdlet `Set-HcsKubeClusterNetworkInfo` pour les modifier.

Vous souhaitez effectuer cette configuration avant de configurer le calcul à partir du portail Azure, car le cluster Kubernetes est créé au cours de cette étape.

1. [Connectez-vous à l'interface PowerShell de l'appareil](#connect-to-the-powershell-interface).
1. À partir de l’interface PowerShell de l’appareil, exécutez :

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Remplacez la valeur <subnet details> par la plage de sous-réseau que vous souhaitez utiliser. 

1. Une fois que vous avez exécuté cette commande, vous pouvez utiliser la commande `Get-HcsKubeClusterNetworkInfo` pour vérifier que les sous-réseaux de pod et de service ont été modifiés.

Voici un exemple de sortie pour cette commande.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Déboguer les problèmes Kubernetes liés à IoT Edge

Avant de commencer, vous devez disposer des éléments suivants :

- Réseau de calcul configuré. Consultez [Tutoriel : configurer le réseau pour Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Rôle de calcul configuré sur votre appareil.
    
Sur un appareil Azure Stack Edge Pro sur lequel le rôle de calcul est configuré, vous pouvez dépanner ou superviser l’appareil à l’aide de deux ensembles de commandes distincts.

- À l’aide des commandes `iotedge`. Ces commandes sont disponibles pour les opérations de base pour votre appareil.
- À l’aide des commandes `kubectl`. Ces commandes sont disponibles pour un ensemble complet d’opérations pour votre appareil.

Pour exécuter les jeux de commandes ci-dessus, vous devez [vous connecter à l’interface PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Utiliser les commandes `iotedge`

Pour afficher la liste des commandes disponibles, [connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface) et utilisez la fonction `iotedge`.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

Le tableau ci-après contient une brève description des commandes disponibles pour `iotedge` :

|command  |Description |
|---------|---------|
|`list`     | Faire la liste des modules         |
|`logs`     | Extraire les journaux d’un module        |
|`restart`     | Arrêter et redémarrer un module         |

#### <a name="list-all-iot-edge-modules"></a>Répertorier tous les modules IoT Edge

Pour répertorier tous les modules en cours d’exécution sur votre appareil, utilisez la commande `iotedge list`.

Voici un exemple de sortie de cette commande. Cette commande répertorie tous les modules, la configuration associée et les adresses IP externes associées aux modules. Par exemple, vous pouvez accéder à l’application **WebServer** à l’adresse URL `https://10.128.44.244`. 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Redémarrer les modules

Vous pouvez utiliser la commande `list` pour répertorier tous les modules en cours d’exécution sur votre appareil. Identifiez ensuite le nom du module que vous souhaitez redémarrer et utilisez-le avec la `restart` commande.

Voici un exemple de sortie de la procédure de redémarrage d’un module. En fonction de la description relative à la durée d’exécution du module, vous pouvez voir que `cuda-sample1` a été redémarré.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Obtenir les journaux des modules

Utilisez la commande `logs` pour obtenir les journaux des modules IoT Edge en cours d’exécution sur votre appareil. 

Si une erreur s’est produite lors de la création de l’image de conteneur ou lors de l’extraction de l’image, exécutez `logs edgeagent`. `edgeagent` est le conteneur de runtime IoT Edge qui est responsable de la mise en service des autres conteneurs. Étant donné que `logs edgeagent` vide les journaux, un bon moyen de voir les erreurs récentes consiste à utiliser l’option `--tail `0`. 

Voici un exemple de sortie.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Utiliser des commandes kubectl

Sur un appareil Azure Stack Edge Pro pour lequel le rôle de calcul est configuré, toutes les commandes `kubectl` sont disponibles pour surveiller ou dépanner les modules. Pour afficher la liste des commandes disponibles, exécutez `kubectl --help` à partir de la fenêtre Commande.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Pour obtenir la liste complète des commandes `kubectl`, accédez à l’[`kubectl` aide-mémoire](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Pour faire en sorte que l’adresse IP d’un service ou d’un module soit exposée en dehors du cluster Kubernetes

Pour obtenir l’adresse IP d’un service d’équilibrage de charge ou de modules exposés en dehors de Kubernetes, exécutez la commande suivante :

`kubectl get svc -n iotedge`

Voici un exemple de sortie de tous les services ou modules exposés en dehors du cluster Kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
L’adresse IP dans la colonne IP externe correspond au point de terminaison externe pour le service ou le module. Vous pouvez également [Récupérer l’adresse IP externe dans le tableau de bord Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Pour vérifier si le module a été déployé avec succès

Les modules de calcul sont des conteneurs avec une logique métier implémentée. Un pod Kubernetes peut avoir plusieurs conteneurs en cours d’exécution. 

Pour vérifier si un module de calcul est correctement déployé, connectez-vous à l’interface PowerShell de l’appareil.
Exécutez la commande `get pods` et vérifiez l’exécution du conteneur (correspondant au module de calcul).

Pour obtenir la liste de tous les pods en cours d’exécution dans un espace de noms spécifique, exécutez la commande suivante :

`get pods -n <namespace>`

Pour vérifier les modules déployés via IoT Edge, exécutez la commande suivante :

`get pods -n iotedge`

Voici un exemple de sortie de tous les pods en cours d’exécution dans l’espace de noms `iotedge`.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

L’**État** indique que tous pods de l’espace de noms sont en cours d’exécution et **Prêt** indique le nombre de conteneurs déployés dans un pod. Dans l’exemple précédent, tous les pods sont en cours d’exécution et tous les modules déployés dans chaque pod sont en cours d’exécution. 

Pour vérifier les modules déployés via Azure Arc, exécutez la commande suivante :

`get pods -n azure-arc`

Vous pouvez également [Vous connecter au tableau de bord Kubernetes pour voir les déploiements IoT Edge ou Azure Arc](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Pour obtenir une sortie plus détaillée d’un pod spécifique pour un espace de noms donné, vous pouvez exécuter la commande suivante :

`kubectl describe pod <pod name> -n <namespace>` 

Un exemple de sortie est présenté ici.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Pour obtenir les journaux d’activité de conteneur

Pour obtenir les journaux d’un module, exécutez la commande suivante à partir de l’interface PowerShell de l’appareil :

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Étant donné que l’indicateur `all-containers` vide les journaux pour tous les conteneurs, un bon moyen de voir les erreurs récentes est d’utiliser l’option `--tail 10`.

Voici un exemple de sortie. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Modifier la mémoire et les limites du processeur pour le nœud Worker Kubernetes

Pour modifier la mémoire ou les limites du processeur pour le nœud Worker Kubernetes, procédez comme suit :

1. [Connectez-vous à l'interface PowerShell de l'appareil](#connect-to-the-powershell-interface).
1. Pour obtenir les ressources actuelles du nœud Worker et les options de rôle, exécutez la commande suivante :

    `Get-AzureDataBoxEdgeRole`

    Voici un exemple de sortie. Notez les valeurs de `Name` et `Compute` dans la section `Resources`. `MemoryInBytes` et `ProcessorCount` indiquent les valeurs de mémoire et de nombre de processeurs pour le nœud Worker Kubernetes.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Pour modifier les valeurs de mémoire et de processeurs pour le nœud Worker, exécutez la commande suivante :

    Set-AzureDataBoxEdgeRoleCompute -Name <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount <Nb de cœurs>

    Voici un exemple de sortie. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Lorsque vous modifiez la mémoire et l’utilisation du processeur, suivez ces instructions.

- La mémoire par défaut correspond à 25 % de la spécification de l’appareil.
- Le nombre de processeurs par défaut représente 30 % de la spécification de l’appareil.
- Quand vous modifiez les valeurs de mémoire et de nombre de processeurs, nous vous recommandons de faire varier les valeurs de mémoire de l’appareil et de nombre de processeurs entre 15 % et 60 %. 
- Nous vous conseillons d’utiliser une limite supérieure de 60 % afin de disposer de suffisamment de ressources pour les composants système. 

## <a name="connect-to-bmc"></a>Se connecter au contrôleur BMC

Le contrôleur de gestion de la carte de base (BMC) vous permet de surveiller et de gérer votre appareil à distance. Cette section décrit les cmdlets qui peuvent être utilisées pour gérer la configuration du contrôleur BMC. Avant d'exécuter l'une de ces cmdlets, consultez [Se connecter à l'interface PowerShell de l'appareil](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface` : Utilisez cette cmdlet pour obtenir les propriétés de configuration réseau du contrôleur BMC, par exemple `IPv4Address`, `IPv4Gateway`, `IPv4SubnetMask`, `DhcpEnabled`. 
    
    Voici un exemple de sortie :
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface` : Vous pouvez utiliser cette cmdlet comme suit :

    - Utilisez la cmdlet pour activer ou désactiver la configuration DHCP du contrôleur BMC en utilisant la valeur appropriée pour le paramètre `UseDhcp`. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Voici un exemple de sortie : 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Utilisez cette cmdlet pour configurer la configuration statique du contrôleur BMC. Vous pouvez spécifier les valeurs des éléments suivants : `IPv4Address`, `IPv4Gateway` et `IPv4SubnetMask`. 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Voici un exemple de sortie : 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword` : Utilisez cette cmdlet pour modifier le mot de passe BMC de `EdgeUser`. Le nom d'utilisateur (`EdgeUser`) est sensible à la casse.

    Voici un exemple de sortie : 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Fermeture de la session à distance

Pour quitter la session PowerShell à distance, fermez la fenêtre PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- Déployer [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) sur le portail Azure.