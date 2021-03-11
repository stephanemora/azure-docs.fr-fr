---
title: Utilisation des extensions de script personnalisé pour des machines virtuelles sur votre appareil Azure Stack Edge Pro
description: Décrit comment installer des extensions de script personnalisé sur des machines virtuelles s’exécutant sur un appareil Azure Stack Edge Pro à l’aide de modèles.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2d2e7d403ab3e9cc7e8e17de53b6e821ec24caa1
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438010"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Déployer des extensions de script personnalisé sur des machines virtuelles s’exécutant sur votre appareil Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

L’extension de script personnalisé télécharge et exécute des scripts ou des commandes sur des machines virtuelles s’exécutant sur vos appareils Azure Stack Edge Pro. Cet article explique en détail comment installer et exécuter l’extension de script personnalisé à l’aide d’un modèle Azure Resource Manager. 

Cet article s'applique aux appareils Azure Stack Edge Pro GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R.

## <a name="about-custom-script-extension"></a>À propos de l’extension de script personnalisé

L’extension de script personnalisé est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration/gestion. Il est possible de télécharger des scripts à partir du Stockage Azure ou de tout autre emplacement Internet accessible, ou de fournir des scripts ou des commandes au runtime de l’extension.

L’extension de script personnalisé est compatible avec les modèles Azure Resource Manager. Elle est également exécutable avec Azure CLI, PowerShell ou l’API REST Machines virtuelles Azure.

## <a name="os-for-custom-script-extension"></a>Système d’exploitation pour l’extension de script personnalisé

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Systèmes d’exploitation pris en charge pour l’extension de script personnalisé sur Windows

L’extension de script personnalisé pour Windows s’exécute sur les systèmes d’exploitation suivants. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles exécutées sur des appareils Azure Stack Edge Pro.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Systèmes d’exploitation pris en charge pour l’extension de script personnalisé sur Linux

L’extension de script personnalisé pour Linux s’exécute sur les systèmes d’exploitation suivants. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles exécutées sur des appareils Azure Stack Edge Pro.

| Distribution | Version |
|---|---|
| Linux : Ubuntu | 18.04 LTS |
| Linux : Red Hat Enterprise Linux | 7.4, 7.5, 7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Prérequis

1. [Téléchargez les modèles de machine virtuelle et les fichiers de paramètres](https://aka.ms/ase-vm-templates) sur votre ordinateur client. Décompressez les fichiers téléchargés dans le répertoire qui vous servira de répertoire de travail.

1. Vous devez disposer d’une machine virtuelle créée et déployée sur votre appareil. Pour créer des machines virtuelles, suivez toutes les étapes de l’article [Déployer une machine virtuelle sur votre appareil Azure Stack Edge Pro à l’aide de modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Si vous devez télécharger en externe un script provenant par exemple de GitHub ou Stockage Azure, lors de la configuration du réseau de calcul, activez le port connecté à Internet pour le calcul. Cela vous permet de télécharger le script.

    Dans l’exemple suivant, le port 2 était connecté à Internet et a été utilisé pour activer le réseau de calcul. Si vous avez constaté que Kubernetes n’est pas nécessaire à l’étape précédente, vous pouvez ignorer l’adresse IP du nœud Kubernetes et l’attribution de l’adresse IP du service externe.

    ![Activer les paramètres de calcul sur le port connecté à Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Installer l’extension de script personnalisé

Selon le système d’exploitation de votre machine virtuelle, vous pouvez installer l’extension de script personnalisé pour Windows ou pour Linux.
 

### <a name="custom-script-extension-for-windows"></a>Extension de script personnalisé pour Windows

Pour déployer l’extension de script personnalisé pour Windows pour une machine virtuelle exécutée sur votre appareil, modifiez le fichier de paramètres `addCSExtWindowsVM.parameters.json`, puis déployez le modèle `addCSextensiontoVM.json`.

#### <a name="edit-parameters-file"></a>Modification du fichier de paramètres

Le fichier `addCSExtWindowsVM.parameters.json` prend les paramètres suivants :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Indiquez le nom de votre machine virtuelle, le nom de l’extension et la commande que vous souhaitez exécuter.

Voici l’exemple de fichier de paramètres utilisé dans cet article.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Déployer un modèle

Déployez le modèle `addCSextensiontoVM.json`. Ce modèle déploie l’extension sur une machine virtuelle existante. Exécutez la commande suivante :

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> Le déploiement de l’extension est une opération durable qui prend environ 10 minutes.

Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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
Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

La sortie de l’extension est enregistrée dans les fichiers qui se trouvent dans le dossier suivant sur la machine virtuelle. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Les fichiers spécifiés sont téléchargés dans le dossier suivant sur la machine virtuelle cible.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
où <n> est un entier décimal, qui peut changer d’une exécution de l’extension à l’autre. La valeur 1.* correspond à la valeur actuelle et réelle `typeHandlerVersion` de l’extension. Par exemple, le répertoire réel de cette instance était `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0`. 


Dans cette instance, la commande à exécuter pour l’extension personnalisée était : `md C:\\Users\\Public\\Documents\\test`. Lorsque l’extension est correctement installée, vous pouvez vérifier que le répertoire a été créé dans la machine virtuelle à l’emplacement spécifié dans la commande. 


### <a name="custom-script-extension-for-linux"></a>Extension de script personnalisé pour Linux

Pour déployer l’extension de script personnalisé pour Windows pour une machine virtuelle exécutée sur votre appareil, modifiez le fichier de paramètres `addCSExtLinuxVM.parameters.json`, puis déployez le modèle `addCSExtensiontoVM.json`.

#### <a name="edit-parameters-file"></a>Modification du fichier de paramètres

Le fichier `addCSExtLinuxVM.parameters.json` prend les paramètres suivants :

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Indiquez le nom de votre machine virtuelle, le nom de l’extension et la commande que vous souhaitez exécuter.

Voici un exemple de fichier de paramètres utilisé dans cet article :

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> Le déploiement de l’extension est une opération durable qui prend environ 10 minutes.

Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

Le paramètre `commandToExecute` a été défini pour créer un fichier `file2.txt` dans le répertoire `/home/Administrator` et le contenu du fichier est `some text`. Dans ce cas, vous pouvez vérifier que le fichier a été créé dans le chemin d’accès spécifié.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Suivre l’état du déploiement    
    
Le déploiement de modèle est une opération durable. Pour suivre l’état du déploiement des extensions pour une machine virtuelle donnée, ouvrez une autre session PowerShell (exécuter en tant qu’administrateur). Exécutez la commande suivante : 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Voici un exemple de sortie : 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant : `/var/lib/waagent/custom-script/download/0/`.


## <a name="remove-custom-script-extension"></a>Supprimer l’extension de script personnalisé

Pour supprimer l’extension de script personnalisé, utilisez la commande suivante :

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Voici un exemple de sortie :

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Étapes suivantes

[Applets de commande Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
