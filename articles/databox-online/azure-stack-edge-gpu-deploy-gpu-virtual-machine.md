---
title: Vue d’ensemble et déploiement des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro
description: Explique comment créer et gérer des machines virtuelles GPU sur un appareil Azure Stack Edge Pro à l’aide de modèles.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 0a7f54d991d31594a6ab522dbf9e73958cde3023
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900142"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Machines virtuelles GPU sur votre appareil Azure Stack Edge Pro

Cet article fournit une vue d’ensemble des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro. Cet article explique comment créer une machine virtuelle GPU, puis installer l’extension du pilote GPU en vue d’installer les pilotes NVIDIA appropriés. Utilisez les modèles Azure Resource Manager pour créer la machine virtuelle GPU et installer l’extension du pilote GPU. 

Cet article s’applique aux appareils Azure Stack Edge Pro GPU et Azure Stack Edge Pro R.

## <a name="about-gpu-vms"></a>À propos des machines virtuelles GPU

Vos appareils Azure Stack Edge Pro sont équipés de 1 ou 2 GPU Tesla T4 de NVIDIA. Pour déployer des charges de travail de machine virtuelle avec accélération GPU sur ces appareils, utilisez des tailles de machine virtuelle optimisées pour le GPU. Par exemple, la série NC T4 v3 doit être utilisée pour déployer des charges de travail d’inférence comprenant des GPU T4. 

Pour plus d’informations, consultez [Machines virtuelles de la série NC T4 v3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Pilotes GPU et systèmes d’exploitation pris en charge 

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU Nvidia. 

L’extension du pilote GPU Nvidia installe les pilotes CUDA ou GRID Nvidia appropriés. Vous pouvez installer ou gérer l’extension à l’aide des modèles Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Systèmes d’exploitation pris en charge pour l’extension GPU pour Windows

Cette extension prend en charge les systèmes d’exploitation suivants. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles GPU exécutées sur des appareils Azure Stack Edge Pro.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Systèmes d’exploitation pris en charge pour l’extension GPU pour Linux

Cette extension prend en charge les distributions suivantes de système d’exploitation, en fonction de la prise en charge par le pilote de la version spécifique du système d’exploitation. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles GPU exécutées sur des appareils Azure Stack Edge Pro.


| Distribution | Version |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>Machines virtuelles GPU et Kubernetes

Avant de déployer des machines virtuelles GPU sur votre appareil, passez en revue les considérations suivantes si Kubernetes est configuré sur l’appareil.

#### <a name="for-1-gpu-device"></a>Pour un appareil avec un GPU : 

- **Procéder à la création d’une machine virtuelle GPU, puis à la configuration de Kubernetes sur votre appareil** : dans ce scénario, la création de la machine virtuelle GPU et la configuration de Kubernetes réussissent. Dans ce cas, Kubernetes n’a pas accès au GPU.

- **Procéder à la configuration de Kubernetes sur votre appareil, puis à la création d’une machine virtuelle GPU** : dans ce scénario, Kubernetes revendique le GPU sur votre appareil et la création de la machine virtuelle échoue, car aucune ressource GPU n’est disponible.

#### <a name="for-2-gpu-device"></a>Pour un appareil avec deux GPU :

- **Procéder à la création d’une machine virtuelle GPU, puis à la configuration de Kubernetes sur votre appareil** : dans ce scénario, la machine virtuelle GPU que vous créez revendique un GPU sur votre appareil et la configuration Kubernetes réussit également et revendique un GPU. 

- **Procéder à la création de deux machines virtuelles GPU, puis à la configuration de Kubernetes sur votre appareil** : Dans ce scénario, les deux machines virtuelles GPU revendiquent les deux GPU sur l’appareil et la configuration de Kubernetes réussit sans GPU. 

- **Procéder à la configuration de Kubernetes sur votre appareil, puis à la création d’une machine virtuelle GPU** : dans ce scénario, Kubernetes revendique les deux GPU sur votre appareil et la création de la machine virtuelle échoue, car aucune ressource GPU n’est disponible.

Si vous avez des machines virtuelles GPU en cours d’exécution sur votre appareil et que Kubernetes est également configuré, chaque fois que la machine virtuelle est désallouée (quand vous arrêtez ou supprimez une machine virtuelle à l’aide de Stop-AzureRmVM ou Remove-AzureRmVM), le cluster Kubernetes risque de revendiquer tous les GPU disponibles sur l’appareil. Dans ce cas, vous ne pouvez pas redémarrer les machines virtuelles GPU déployées sur votre appareil ou créer des machines virtuelles GPU.


## <a name="create-gpu-vms"></a>Créer des machines virtuelles GPU

Pour déployer des machines virtuelles GPU sur votre appareil, procédez comme suit :

1. Identifiez si votre appareil doit également exécuter Kubernetes. Si l’appareil doit exécuter Kubernetes, vous devez d’abord créer la machine virtuelle GPU, puis configurer Kubernetes. Si Kubernetes est configuré en premier, il revendique toutes les ressources GPU disponibles et la création de la machine virtuelle GPU échoue.

1. [Téléchargez les modèles de machine virtuelle et les fichiers de paramètres](https://aka.ms/ase-vm-templates) sur votre ordinateur client. Décompressez-les dans le répertoire qui vous servira de répertoire de travail.

1. Pour créer des machines virtuelles GPU, suivez toutes les étapes de l’article [Déployer une machine virtuelle sur votre appareil Azure Stack Edge Pro à l’aide de modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md), sauf pour les points particuliers suivants : 

    1. Lors de la configuration du réseau de calcul, activez le port connecté à Internet pour le calcul. Cela vous permet de télécharger les pilotes GPU nécessaires aux extensions GPU pour vos machines virtuelles GPU.

        Dans l’exemple suivant, le port 2 était connecté à Internet et a été utilisé pour activer le réseau de calcul. Si vous avez constaté que Kubernetes n’est pas nécessaire à l’étape précédente, vous pouvez ignorer l’adresse IP du nœud Kubernetes et l’attribution de l’adresse IP du service externe.

        ![Activer les paramètres de calcul sur le port connecté à Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Créez une machine virtuelle à l’aide des modèles. Quand vous spécifiez des tailles de machine virtuelle GPU, veillez à utiliser la série NCasT4-v3 dans `CreateVM.parameters.json`, car elles sont prises en charge pour les machines virtuelles GPU. Pour plus d’informations, consultez [Tailles de machine virtuelle prises en charge pour les machines virtuelles GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Une fois la machine virtuelle GPU créée, elle apparaît dans la liste des machines virtuelles de votre ressource Azure Stack Edge dans le portail Azure.

        ![Machine virtuelle GPU dans la liste des machines virtuelles dans le portail Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Sélectionnez la machine virtuelle pour afficher ses détails. Copiez l’adresse IP allouée à la machine virtuelle.

    ![Adresse IP allouée à la machine virtuelle GPU dans le portail Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Une fois la machine virtuelle créée, déployez l’extension GPU à l’aide du modèle d’extension. Pour les machines virtuelles Linux, consultez [Installer l’extension GPU pour Linux](#gpu-extension-for-linux) et, pour les machines virtuelles Windows, consultez [Installer l’extension GPU pour Windows](#gpu-extension-for-windows).

1. Pour vérifier l’installation de l’extension GPU, connectez-vous à la machine virtuelle GPU :
    1. Si vous utilisez une machine virtuelle Windows, suivez les étapes décrites dans [Se connecter à une machine virtuelle Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm). [Vérifiez l’installation](#verify-windows-driver-installation).
    1. Si vous utilisez une machine virtuelle Linux, suivez les étapes décrites dans [Se connecter à une machine virtuelle Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). [Vérifiez l’installation](#verify-linux-driver-installation).

1. Si nécessaire, vous pouvez rétablir le réseau de calcul sur la configuration requise. 


> [!NOTE]
> Lors de la mise à jour de la version logicielle de votre appareil depuis la version 2012 vers une version ultérieure, vous devez arrêter manuellement les machines virtuelles GPU.


## <a name="install-gpu-extension"></a>Installer l’extension GPU

Selon le système d’exploitation de votre machine virtuelle, vous pouvez installer l’extension GPU pour Windows ou pour Linux.

> [!NOTE]
> Avant d’installer l’extension GPU, assurez-vous que le port activé pour le réseau de calcul sur votre appareil est connecté à Internet et qu’il y a accès. Les pilotes GPU sont téléchargés via l’accès à Internet.

### <a name="gpu-extension-for-windows"></a>Extension GPU pour Windows

Pour déployer des pilotes GPU Nvidia pour une machine virtuelle existante, modifiez le fichier de paramètres `addGPUExtWindowsVM.parameters.json`, puis déployez le modèle `addGPUextensiontoVM.json`.

#### <a name="edit-parameters-file"></a>Modification du fichier de paramètres

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

Voici un exemple de fichier de paramètres utilisé dans cet article :

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Déployer un modèle

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
#### <a name="track-deployment"></a>Suivre le déploiement

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

#### <a name="verify-windows-driver-installation"></a>Vérifier l’installation du pilote Windows

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

### <a name="gpu-extension-for-linux"></a>Extension GPU pour Linux

Pour déployer des pilotes GPU Nvidia pour une machine virtuelle existante, modifiez le fichier de paramètres, puis déployez le modèle `addGPUextensiontoVM.json`. Il existe des fichiers de paramètres spécifiques pour Ubuntu et Red Hat Enterprise Linux (RHEL), comme indiqué dans les sections suivantes.

#### <a name="edit-parameters-file"></a>Modification du fichier de paramètres

Si vous utilisez Ubuntu, le fichier `addGPUExtLinuxVM.parameters.json` prend les paramètres suivants :

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
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Si vous utilisez Red Hat Enterprise Linux (RHEL), le fichier `addGPUExtensionRHELVM.parameters.json` prend les paramètres suivants :

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
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
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
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
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Déployer un modèle

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

#### <a name="track-deployment-status"></a>Suivre l’état du déploiement    
    
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

La sortie de l’exécution de l’extension est journalisée dans le fichier suivant : `/var/log/azure/nvidia-vmext-status`.

#### <a name="verify-linux-driver-installation"></a>Vérifier l’installation du pilote Linux

Pour vérifier l’installation du pilote, procédez comme suit :

1. Connectez-vous à la machine virtuelle GPU. Suivez les instructions indiquées dans [Se connecter à une machine virtuelle Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). 

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

[Applets de commande Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)