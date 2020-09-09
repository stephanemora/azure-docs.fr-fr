---
title: Se connecter à un appareil Microsoft Azure Stack Edge et le gérer via l’interface Windows PowerShell | Microsoft Docs
description: Décrit comment se connecter à un appareil Azure Stack Edge et le gérer via l’interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 8b654c45fa35047e874103ff84655c268f77aa24
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294881"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Gérer un appareil Azure Stack Edge avec GPU via Windows PowerShell

La solution Azure Stack Edge vous permet de traiter des données et de les envoyer à Azure via le réseau. Cet article décrit certaines des tâches de gestion et de configuration pour votre appareil Azure Stack Edge. Vous pouvez utiliser le portail Azure, l'interface utilisateur locale ou l'interface Windows PowerShell pour gérer votre appareil.

Cet article se concentre sur la façon dont vous pouvez vous connecter à l’interface PowerShell de l’appareil et les tâches que vous pouvez effectuer à l’aide de cette interface. 


## <a name="connect-to-the-powershell-interface"></a>Connexion à l’interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) or [Install certificates on a gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).-->

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

Un service multiprocessus (MPS) sur les GPU Nvidia fournit un mécanisme dans lequel les GPU peuvent être partagés par plusieurs tâches, où chaque tâche est se voit attribuer un certain pourcentage des ressources du GPU. Pour activer un MPS sur votre appareil Azure Stack Edge, procédez comme suit :

1. Avant de commencer, assurez-vous que : 

    1. Vous avez configuré et [Activé votre appareil Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md) avec une ressource Azure Stack Edge/Data Box Gateway dans Azure.
    1. Vous avez [Configuré le calcul sur cet appareil dans le portail Azure](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
1. Utilisez la commande suivante pour activer un MPS sur votre appareil.

    ```powershell
    Start-HcsGpuMPS
    ```

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


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Déboguer les problèmes Kubernetes liés à IoT Edge

Lorsque le cluster Kubernetes est créé, deux espaces de noms système sont créés : `iotedge` et `azure-arc`.  

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

On an Azure Stack Edge device that has the compute role configured, all the `kubectl` commands are available to monitor or troubleshoot modules. To see a list of available commands, run `kubectl --help` from the command window.

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

For a comprehensive list of the `kubectl` commands, go to [`kubectl` cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).-->


### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Pour faire en sorte que l’adresse IP d’un service ou d’un module soit exposée en dehors du cluster Kubernetes

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


### <a name="to-check-if-module-deployed-successfully"></a>Pour vérifier si le module a été déployé avec succès

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

### <a name="to-get-container-logs"></a>Pour obtenir les journaux d’activité de conteneur

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

## <a name="exit-the-remote-session"></a>Fermeture de la session à distance

Pour quitter la session PowerShell à distance, fermez la fenêtre PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- Déployez [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) sur le portail Azure.
