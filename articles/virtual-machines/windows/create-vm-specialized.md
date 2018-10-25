---
title: Créer une machine virtuelle Windows à partir d’un disque dur virtuel spécialisé dans Azure | Microsoft Docs
description: Créez une machine virtuelle Windows en attachant un disque managé spécialisé comme disque du système d’exploitation à l’aide du modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 53062ee6384113ef8c483bc9cc6b407559c35994
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406124"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Créer une machine virtuelle Windows à partir d’un disque spécialisé à l’aide de PowerShell

Créez une machine virtuelle en attachant un disque managé spécialisé en tant que disque du système d’exploitation. Un disque spécialisé est une copie d’un disque dur virtuel d’une machine virtuelle existante qui contient les comptes d’utilisateurs, applications et autres données d’état de votre machine virtuelle d’origine. 

Lorsque vous utilisez un disque dur virtuel spécialisé pour créer une machine virtuelle, la nouvelle machine conserve le nom d’ordinateur de la machine virtuelle d’origine. D’autres informations spécifiques à l’ordinateur sont également conservées. Dans certains cas, ces informations en double peuvent entraîner des problèmes. Quand vous copiez une machine virtuelle, vous devez connaître les types d’informations spécifiques à l’ordinateur dont vos applications dépendent.

Vous disposez de plusieurs options :
* [Utilisez un disque managé existant](#option-1-use-an-existing-disk). Cette option est utile si vous avez une machine virtuelle qui ne fonctionne pas correctement. Vous pouvez supprimer la machine virtuelle, puis réutiliser le disque managé pour créer une machine virtuelle. 
* [Charger un disque dur virtuel](#option-2-upload-a-specialized-vhd) 
* [Copier une machine virtuelle Azure existante à l’aide de captures instantanées](#option-3-copy-an-existing-azure-vm)

Vous pouvez également utiliser le portail Azure pour [créer une machine virtuelle à partir d’un disque dur virtuel spécialisé](create-vm-specialized-portal.md).

Cet article montre comment utiliser des disques managés. Si vous avez un déploiement hérité qui nécessite l’utilisation d’un compte de stockage, consultez [Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé dans un compte de stockage](sa-create-vm-specialized.md).

## <a name="before-you-begin"></a>Avant de commencer
Pour utiliser PowerShell, vérifiez que vous disposez de la dernière version du module PowerShell AzureRM.Compute. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Pour plus d’informations, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Option 1 : Utiliser un disque existant

Si vous avez une machine virtuelle que vous avez supprimée et que vous souhaitez réutiliser le disque du système d’exploitation pour créer une machine virtuelle, utilisez [Get-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Vous pouvez maintenant joindre ce disque en tant que disque de système d’exploitation à une [nouvelle machine virtuelle](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Option 2 : Charger un disque dur virtuel spécialisé

Vous pouvez charger le disque dur virtuel à partir d’une machine virtuelle spécialisée créée avec un outil de virtualisation local tel que Hyper-V, ou d’une machine virtuelle exportée à partir d’un autre cloud.

### <a name="prepare-the-vm"></a>Préparation de la machine virtuelle
Utilisez le disque dur virtuel en l’état pour créer une machine virtuelle. 
  
  * [Préparez un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep.
  * Supprimez tous les outils et agents de virtualisation invités qui sont installés sur la machine virtuelle (notamment les outils VMware).
  * Vérifiez que la machine virtuelle est configurée pour obtenir l’adresse IP et les paramètres DNS à partir de DHCP. De cette façon, le serveur obtient une adresse IP dans le réseau virtuel au démarrage. 


### <a name="get-the-storage-account"></a>Obtention du compte de stockage
Vous avez besoin d’un compte de stockage dans Azure pour stocker le disque dur virtuel chargé. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Affichez les comptes de stockage disponibles.

```powershell
Get-AzureRmStorageAccount
```

Pour utiliser un compte de stockage existant, passez à la section [Charger le disque dur virtuel](#upload-the-vhd-to-your-storage-account).

Créez un compte de stockage.

1. Vous avez besoin du nom du groupe de ressources dans lequel doit être créé le compte de stockage. Utilisez Get-AzureRmResourceGroup pour afficher tous les groupes de ressources dans votre abonnement.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Créez un groupe de ressources nommé *MyResourceGroup* dans la région *USA Ouest*.

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Créez un compte de stockage nommé *mystorageaccount* dans le nouveau groupe de ressources à l’aide de l’applet de commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage 
Utilisez l’applet de commande [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) pour charger le disque dur virtuel vers un conteneur de votre compte de stockage. Cet exemple charge le fichier *myVHD.vhd* à partir de "C:\Users\Public\Documents\Virtual\" sur un compte de stockage nommé *mystorageaccount* dans le groupe de ressources *myResourceGroup*. Le fichier est placé dans le conteneur nommé *mycontainer*, et le nouveau nom de fichier est *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si les commandes réussissent, vous obtenez une réponse semblable à celle-ci :

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Cette commande peut prendre du temps, selon votre connexion réseau et la taille de votre fichier VHD.

### <a name="create-a-managed-disk-from-the-vhd"></a>Créer un disque géré à partir du disque dur virtuel

Créez un disque managé à partir du disque dur virtuel spécialisé dans votre compte de stockage à l’aide de [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Cet exemple utilise *myOSDisk1* pour le nom du disque, place le disque dans le stockage *Standard_LRS* et utilise *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* comme URI du disque dur virtuel source.

Créez un groupe de ressources pour la nouvelle machine virtuelle.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Créez le disque de système d’exploitation à partir du disque dur virtuel chargé. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Option 3 : Copier une machine virtuelle Azure existante

Vous pouvez créer une copie d’une machine virtuelle qui utilise des disques managés en prenant une capture instantanée de la machine virtuelle, puis en utilisant cette capture instantanée pour créer un disque managé et une machine virtuelle.


### <a name="take-a-snapshot-of-the-os-disk"></a>Prendre une capture instantanée du disque de système d’exploitation

Vous pouvez prendre une capture instantanée d’une machine virtuelle entière (y compris tous les disques) ou d’un seul disque. Les étapes suivantes montrent comment prendre une capture instantanée uniquement du disque du système d’exploitation de votre machine virtuelle à l’aide de l’applet de commande [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Commencez par définir des paramètres. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obtenez l’objet machine virtuelle.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Obtenez le nom du disque de système d’exploitation.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Créez la configuration de capture instantanée. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Prenez la capture instantanée.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Pour utiliser cette capture instantanée afin de créer une machine virtuelle hautement performante, ajoutez le paramètre `-AccountType Premium_LRS` à la commande New-AzureRmSnapshot. Ce paramètre crée la capture instantanée et la stocke en tant que disque managé Premium. Les disques managés Premium sont plus chers que les disques Standard. Vérifiez donc que vous avez besoin de disques Premium avant d’utiliser ce paramètre.

### <a name="create-a-new-disk-from-the-snapshot"></a>Créer un disque à partir de la capture instantanée

Créez un disque managé à partir de la capture instantanée en utilisant [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Cet exemple utilise *myOSDisk* comme nom du disque.

Créez un groupe de ressources pour la nouvelle machine virtuelle.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Définissez le nom du disque de système d’exploitation. 

```powershell
$osDiskName = 'myOsDisk'
```

Créez le disque géré.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Créer la machine virtuelle 

Créez la mise en réseau et d’autres ressources de machine virtuelle que la nouvelle machine virtuelle doit utiliser.

### <a name="create-the-subnet-and-virtual-network"></a>Créer le sous-réseau et le réseau virtuel

Créez le [réseau virtuel](../../virtual-network/virtual-networks-overview.md) et le sous-réseau de la machine virtuelle.

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé *mySubnet* dans le groupe de ressources *myDestinationResourceGroup*, et définit le préfixe d’adresse de sous-réseau sur *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Création du réseau virtuel. Cet exemple définit *myVnetName* comme nom de réseau virtuel, *USA Ouest* comme emplacement et *10.0.0.0/16* comme préfixe d’adresse du réseau virtuel. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP
Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP (Remote Desktop Protocol), vous devez disposer d’une règle de sécurité qui autorise l’accès RDP sur le port 3389. Dans notre exemple, le disque dur virtuel de la nouvelle machine virtuelle a été créé à partir d’une machine virtuelle spécialisée. Vous pouvez donc utiliser un compte de la machine virtuelle source pour le protocole RDP.

Cet exemple donne au groupe de sécurité réseau le nom *myNsg* et à la règle RDP le nom *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Pour plus d’informations sur les points de terminaison et les règles NSG, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Créer une adresse IP publique et une carte réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez l’adresse IP publique. Dans cet exemple, le nom d’adresse IP publique est défini sur *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Créez la carte réseau. Dans cet exemple, le nom de carte réseau est défini sur *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Définir le nom et la taille de machine virtuelle

Cet exemple définit *myVM* en tant que nom de machine virtuelle, et *Standard_A2* pour la taille de celle-ci.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Ajouter la carte réseau
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Ajouter le disque de système d’exploitation 

Ajoutez le disque du système d’exploitation à la configuration en utilisant la commande [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Cet exemple définit la taille du disque sur *128 Go* et attache le disque géré en tant que disque de système d’exploitation *Windows*.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Terminer la machine virtuelle 

Créez la machine virtuelle en utilisant [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) avec les configurations que nous venons de créer.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Si cette commande aboutit, vous obtenez une sortie similaire à celle-ci :

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Vous devez voir la machine virtuelle récemment créée soit dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles**, soit en utilisant les commandes PowerShell suivantes.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Connectez-vous à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md).

