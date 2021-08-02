---
title: Installer l’extension GPU sur les machines virtuelles GPU de votre appareil Azure Stack Edge Pro avec GPU
description: Décrit comment installer des extensions GPU sur les machines virtuelles GPU d’un appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/27/2021
ms.author: alkohli
ms.openlocfilehash: 76e1f80e1c6e1d977521724959db6ad36694f238
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110684115"
---
# <a name="install-gpu-extension-on-vms-for-your-azure-stack-edge-pro-gpu-device"></a>Installer l’extension GPU sur des machines virtuelles pour votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Cet article explique comment installer l’extension du pilote GPU pour installer les pilotes NVIDIA appropriés sur les machines virtuelles GPU s’exécutant sur votre appareil Azure Stack Edge. L’article couvre les étapes d’installation de l’extension GPU sur les machines virtuelles Windows et Linux.


## <a name="prerequisites"></a>Prérequis

Avant d’installer l’extension GPU sur les machines virtuelles GPU exécutées sur votre appareil, assurez-vous que :

1. Vous avez accès à un appareil Azure Stack Edge sur lequel vous avez déployé une ou plusieurs machines virtuelles GPU. Sachez comment [Déployer une machine virtuelle GPU sur votre appareil](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).

    - Assurez-vous que le port activé pour le réseau de calcul sur votre appareil est connecté à Internet et qu’il y a accès. Les pilotes GPU sont téléchargés via l’accès à Internet.

        Dans l’exemple suivant, le port 2 était connecté à Internet et a été utilisé pour activer le réseau de calcul. Si Kubernetes n’est pas déployé sur votre environnement, vous pouvez ignorer l’IP du nœud Kubernetes et l’attribution de l’IP du service externe.

        ![Activer les paramètres de calcul sur le port connecté à Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)
1. [Téléchargez les fichiers de paramètres et les modèles d’extension GPU](https://aka.ms/ase-vm-templates) sur votre ordinateur client. Décompressez-les dans le répertoire qui vous servira de répertoire de travail.
1. Vérifiez que le client que vous allez utiliser pour accéder à votre appareil est toujours connecté à l’instance Azure Resource Manager sur Azure PowerShell. La connexion à Azure Resource Manager expire toutes les 1,5 heures ou si votre appareil Azure Stack Edge redémarre. Dans ce cas, toute cmdlet que vous exécutez retourne un message d’erreur indiquant que vous n’êtes plus connecté à Azure. Vous devez vous reconnecter. Pour obtenir des instructions détaillées, consultez [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-connect-resource-manager.md).



## <a name="edit-parameters-file"></a>Modification du fichier de paramètres

Selon le système d’exploitation de votre machine virtuelle, vous pouvez installer l’extension GPU pour Windows ou pour Linux.


### <a name="windows"></a>[Windows](#tab/windows)

Pour déployer des pilotes GPU Nvidia pour une machine virtuelle existante, modifiez le fichier de paramètres `addGPUExtWindowsVM.parameters.json`, puis déployez le modèle `addGPUextensiontoVM.json`.

Le fichier `addGPUExtWindowsVM.parameters.json` prend les paramètres suivants :

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

### <a name="linux"></a>[Linux](#tab/linux)

Pour déployer des pilotes GPU Nvidia pour une machine virtuelle Linux existante, modifiez le fichier de paramètres, puis déployez le modèle `addGPUextensiontoVM.json`. 

Si vous utilisez Ubuntu ou Red Hat Enterprise Linux (RHEL), le fichier `addGPUExtLinuxVM.parameters.json` prend les paramètres suivants :

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    }
    }
    }
```

Voici un exemple de fichier de paramètres Ubuntu utilisé dans cet article :

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
        }
    }
}
```

### <a name="gpu-vms-from-rhel-byos-images"></a>Machines virtuelles GPU provenant d’images RHEL BYOS

Si vous avez créé votre machine virtuelle au moyen d’une image RHEL BYOS (Red Hat Enterprise Linux Bring Your Own Subscription), assurez-vous que :

- Vous avez suivi les étapes de la section [Utilisation de l’image RHEL BYOS](azure-stack-edge-gpu-create-virtual-machine-image.md). 
- Après avoir créé la machine virtuelle GPU, inscrivez-la et abonnez-la au portail client Red Hat. Si votre machine virtuelle n’est pas correctement inscrite, l’installation ne se poursuit pas, car la machine virtuelle n’est pas autorisée. Consultez [S’inscrire et s’abonner automatiquement en une étape à l’aide du gestionnaire d’abonnements Red Hat](https://access.redhat.com/solutions/253273). Cette étape permet au script d’installation de télécharger les packages appropriés pour le pilote GPU.
- Vous pouvez soit installer manuellement le package `vulkan-filesystem`, soit ajouter le référentiel CentOS7 à votre liste de référentiels yum. Lorsque vous installez l’extension GPU, le script d’installation recherche un package `vulkan-filesystem` qui se trouve sur le référentiel CentOS7 (pour RHEL7). 

---

## <a name="deploy-template"></a>Déployer un modèle 

### <a name="windows"></a>[Windows](#tab/windows)

Déployez le modèle `addGPUextensiontoVM.json`. Ce modèle déploie l’extension sur une machine virtuelle existante. Exécutez la commande suivante :

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> Le déploiement de l’extension est une opération durable qui prend environ 10 minutes.

Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

### <a name="linux"></a>[Linux](#tab/linux)

Déployez le modèle `addGPUextensiontoVM.json`. Ce modèle déploie l’extension sur une machine virtuelle existante. Exécutez la commande suivante :

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> Le déploiement de l’extension est une opération durable qui prend environ 10 minutes.

Voici un exemple de sortie :

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
---

## <a name="track-deployment"></a>Suivre le déploiement

### <a name="windows"></a>[Windows](#tab/windows)

Pour voir l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante : 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant. Reportez-vous à ce fichier `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` pour suivre l’état de l’installation. 


Quand une installation a réussi, `message` a pour valeur `Enable Extension` et `status` a pour valeur `success`.

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

### <a name="linux"></a>[Linux](#tab/linux)

Le déploiement de modèle est une opération durable. Pour suivre l’état du déploiement des extensions pour une machine virtuelle donnée, ouvrez une autre session PowerShell (exécuter en tant qu’administrateur). Exécutez la commande suivante : 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Voici un exemple de sortie : 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> À la fin du déploiement, l’élément `ProvisioningState` indique `Succeeded`.

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant : `/var/log/azure/nvidia-vmext-status`.

---

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote

### <a name="windows"></a>[Windows](#tab/windows)

Connectez-vous à la machine virtuelle et exécutez l’utilitaire en ligne de commande nvidia-smi installé avec le pilote. Le fichier `nvidia-smi.exe` se trouve à l’emplacement `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe`. Si vous ne voyez pas le fichier, il est possible que l’installation du pilote soit toujours en cours d’exécution en arrière-plan. Attendez 10 minutes, puis vérifiez à nouveau.

Si le pilote est installé, le résultat obtenu ressemble à l’exemple suivant : 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Pour plus d’informations, consultez [Extension du pilote GPU Nvidia pour Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="linux"></a>[Linux](#tab/linux)

Pour vérifier l’installation du pilote, procédez comme suit :

1. Connectez-vous à la machine virtuelle GPU. Suivez les instructions indiquées dans [Se connecter à une machine virtuelle Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Exécutez l’utilitaire en ligne de commande nvidia-smi installé avec le pilote. Si le pilote est correctement installé, vous pouvez exécuter l’utilitaire et obtenir la sortie suivante :

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Pour plus d’informations, consultez [Extension du pilote GPU Nvidia pour Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

---


## <a name="remove-gpu-extension"></a>Supprimer l’extension GPU

Pour supprimer l’extension GPU, utilisez la commande suivante :

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Voici un exemple de sortie :

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

- [Gérer les disques de machine virtuelle](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md).
- [Gérer les interfaces réseau machines virtuelles](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).
- [Gérer les tailles de machine virtuelle](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md).