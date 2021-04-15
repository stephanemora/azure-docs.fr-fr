---
title: Créer des images de machine virtuelle à partir d’une image spécialisée du disque dur virtuel Windows pour votre appareil GPU Azure Stack Edge Pro
description: Décrit comment créer des images de machine virtuelle à partir d’images spécialisées à partir d’un disque dur virtuel Windows ou d’un VHDX. Utilisez cette image spécialisée pour créer des images de machine virtuelle à utiliser avec les machines virtuelles déployées sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555840"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Déployer une machine virtuelle à partir d’une image spécialisée sur votre appareil Azure Stack Edge Pro via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit les étapes nécessaires pour déployer une machine virtuelle sur votre appareil Azure Stack Edge Pro à partir d’une image spécialisée. 

## <a name="about-specialized-images"></a>À propos des images spécialisées

Un disque dur virtuel Windows ou un VHDX peut être utilisé pour créer une image *spécialisée* ou une image *généralisée*. Le tableau suivant récapitule les principales différences entre les images *spécialisées* et *généralisées*.


|Type d’image  |Généralisée  |Spécialisée  |
|---------|---------|---------|
|Cible     |Déployée sur n’importe quel système         | Ciblée sur un système spécifique        |
|Installation après le démarrage     | Installation requise au premier démarrage de la machine virtuelle.          | Installation non nécessaire. <br> La plateforme active la machine virtuelle.        |
|Configuration     |Nom d’hôte, utilisateur admin et autres paramètres spécifiques à la machine virtuelle obligatoires.         |Préconfiguré.         |
|Usage     |Créer plusieurs nouvelles machines virtuelles à partir de la même image.         |Migrer une machine spécifique ou restauration d’une machine virtuelle à partir d’une sauvegarde précédente.         |


Cet article décrit les étapes nécessaires au déploiement à partir d’une image spécialisée. Pour déployer à partir d’une image généralisée, consultez [Utiliser un disque dur virtuel Windows généralisé](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) pour votre appareil.


## <a name="vm-image-workflow"></a>Workflow d’image de machine virtuelle

Le flux de travail de haut niveau pour déployer une machine virtuelle à partir d’une image spécialisée est le suivant :

1. Copiez le disque dur virtuel dans un compte de stockage local sur votre appareil GPU Azure Stack Edge Pro.
1. Créez un disque managé géré à partir du disque dur virtuel.
1. Créez une machine virtuelle à partir du disque managé, puis attachez le disque managé.


## <a name="prerequisites"></a>Prérequis

Avant de pouvoir déployer une machine virtuelle sur votre appareil via PowerShell, vérifiez les éléments suivants :

- Vous avez accès à un client que vous utiliserez pour vous connecter à votre appareil.
    - Votre client exécute un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Votre client est configuré pour se connecter à l’instance Azure Resource Manager locale de votre appareil, conformément aux instructions fournies dans [Se connecter à Azure Resource Manager pour votre appareil](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Vérifier la connexion locale à l’instance Azure Resource Manager

Vérifiez que votre client peut se connecter à l’instance Azure Resource Manager locale. 

1. Appelez les API de l’appareil local pour l’authentification :

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Indiquez le nom d’utilisateur `EdgeArmUser` et le mot de passe pour vous connecter via Azure Resource Manager. Si vous avez oublié le mot de passe, consultez [Réinitialiser le mot de passe pour Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) et utilisez le nouveau mot de passe pour vous connecter.
 

## <a name="deploy-vm-from-specialized-image"></a>Déployer une machine virtuelle à partir d’une image spécialisée

Les sections suivantes contiennent des instructions pas à pas pour déployer une machine virtuelle à partir d’une image spécialisée.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Copier le disque dur virtuel vers le compte de stockage local sur l’appareil

Procédez comme suit pour copier le disque dur virtuel vers le compte de stockage local :

1. Copiez le disque dur virtuel source dans un compte de stockage blob local sur votre appareil Azure Stack Edge. 

1. Prenez note de l’URI qui en résulte. Vous utiliserez cet URI plus tard.
    
    Pour créer un compte de stockage local et y accéder, consultez les sections [Créer un compte de stockage](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) via [Charger un disque dur virtuel](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) dans l’article : [Déployer des machines virtuelles sur votre appareil Azure Stack Edge via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Créer un disque managé à partir d’un disque dur virtuel

Procédez comme suit pour créer un disque managé à partir d’un disque dur virtuel que vous avez chargé vers le compte de stockage précédemment :

1. Définissez certains paramètres.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Créez un disque managé.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Voici un exemple de sortie : L’emplacement est ici défini sur l’emplacement du compte de stockage local et il s’agit toujours de `DBELocal` pour tous les comptes de stockage locaux sur votre appareil GPU Azure Stack Edge Pro. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Créer une machine virtuelle à partir d’un disque managé

Pour créer une machine virtuelle à partir d’un disque managé, procédez comme suit :

1. Définissez certains paramètres.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > Le paramètre `PrivateIP` est facultatif. Utilisez ce paramètre pour affecter une adresse IP statique, sans quoi la valeur par défaut est une adresse IP dynamique utilisant DHCP.

    Voici un exemple de sortie : Dans cet exemple, le même groupe de ressources est spécifié pour toutes les ressources de machine virtuelle, mais vous pouvez créer et spécifier des groupes de ressources distincts pour les ressources, si nécessaire.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Obtenez les informations sur le réseau virtuel et créez une interface réseau.

    Cet exemple suppose que vous créez une seule interface réseau sur le réseau virtuel par défaut `ASEVNET` qui est associé au groupe de ressources par défaut `ASERG`. Si nécessaire, vous pouvez spécifier un autre réseau virtuel ou créer plusieurs interfaces réseau. Pour plus d’informations, consultez [Ajouter une interface réseau à une machine virtuelle via le portail Azure](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Créez un objet de configuration de machine virtuelle.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Ajoutez l’interface réseau à la machine virtuelle.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Définissez les propriétés du disque du système d’exploitation sur la machine virtuelle.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Le dernier indicateur de cette commande est `-Windows` ou `-Linux` selon le système d’exploitation que vous utilisez pour votre machine virtuelle.

1. Créez la machine virtuelle.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Voici un exemple de sortie : 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Supprimer la machine virtuelle et les ressources

Cet article a utilisé un seul groupe de ressources pour créer toutes les ressources de machine virtuelle. La suppression de ce groupe de ressources entraîne la suppression de la machine virtuelle et toutes les ressources associées. 

1. Tout d’abord, affichez toutes les ressources créées sous le groupe de ressources.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Voici un exemple de sortie :
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Supprimez le groupe de ressources et toutes les ressources associées.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Voici un exemple de sortie :
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Vérifiez que le groupe de ressources a été supprimé. Récupérez tous les groupes de ressources qui existent sur l’appareil. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Étapes suivantes

En fonction de la nature du déploiement, vous pouvez choisir l’une des procédures suivantes.

- [Déployer une machine virtuelle à partir d’une image généralisée via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Déployer une machine virtuelle via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
