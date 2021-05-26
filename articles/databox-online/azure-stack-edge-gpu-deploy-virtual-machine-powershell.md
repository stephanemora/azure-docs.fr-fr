---
title: Déployer des machines virtuelles sur votre appareil Azure Stack Edge via Azure PowerShell
description: Décrit comment créer et gérer des machines virtuelles sur un appareil Azure Stack Edge à l’aide d’Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 94ebfcab77b6db579b263dc11f856612bb2c630a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110086317"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Déployer des machines virtuelles sur votre appareil Azure Stack Edge via Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment créer et gérer une machine virtuelle sur votre appareil Azure Stack Edge à l’aide d’Azure PowerShell. Les informations s’appliquent aux appareils Azure Stack Edge Pro avec GPU (unité de traitement graphique), Azure Stack Edge Pro R et Azure Stack Edge Mini R.

## <a name="vm-deployment-workflow"></a>Workflow du déploiement de machine virtuelle

Le workflow du déploiement est affiché dans le diagramme suivant :

![Diagramme du workflow de déploiement d’une machine virtuelle.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Rechercher un abonnement intégré sur l’appareil

Pour Azure Resource Manager, un seul abonnement fixe visible par l’utilisateur est pris en charge. Cet abonnement est unique par appareil, et le nom et l’ID de l’abonnement ne peuvent pas être modifiés.

L’abonnement contient toutes les ressources nécessaires à la création de la machine virtuelle. 

> [!IMPORTANT]
> L’abonnement est créé quand vous activez des machines virtuelles à partir du portail Azure, et il réside localement sur votre appareil.

L’abonnement est utilisé pour déployer les machines virtuelles.

1.  Pour lister l’abonnement, exécutez la commande suivante :

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Voici un exemple de sortie :

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Obtenez une liste des fournisseurs de ressources inscrits qui s’exécutent sur l’appareil. La liste comprend habituellement le calcul, le réseau et le stockage.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Les fournisseurs de ressources sont préinscrits et ne peuvent pas être modifiés.
    
    Voici un exemple de sortie :

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel sont déployées et gérées les ressources Azure, comme un compte de stockage, un disque et un disque managé.

> [!IMPORTANT]
> Toutes les ressources sont créées au même emplacement que l’appareil, à savoir **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Voici un exemple de sortie :

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage en utilisant le groupe de ressources créé à l’étape précédente. Il s’agit d’un compte de stockage local qui sera utilisé pour charger l’image de disque virtuel de la machine virtuelle.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Avec Azure Resource Manager, vous pouvez uniquement créer des comptes de stockage locaux, tels que le stockage localement redondant (Standard ou Premium). Pour créer des comptes de stockage hiérarchisés, consultez [Tutoriel : Transférer des données par le biais de comptes de stockage avec Azure Stack Edge Pro avec GPU](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

Voici un exemple de sortie :

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Pour obtenir la clé du compte de stockage, exécutez la commande `Get-AzureRmStorageAccountKey`. Voici un exemple de sortie :

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Ajouter l’URI d’objet blob au fichier hôte

Vous avez déjà ajouté l’URI de blob dans le fichier hosts du client que vous utilisez pour vous connecter au stockage Blob Azure dans « Étape 5 : Modifier le fichier hosts pour la résolution de noms de point de terminaison » de [Déployer des machines virtuelles sur votre appareil Azure Stack Edge via Azure PowerShell](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Cette entrée a été utilisée pour ajouter l’URI de l’objet blob :

\<Azure consistent network services VIP \> \<storage name\>.blob.\<appliance name\>.\<dnsdomain\>

## <a name="install-certificates"></a>Installer des certificats

Si vous utilisez HTTPS, vous devez installer les certificats appropriés sur votre appareil. Ici, vous installez le certificat du point de terminaison de blob. Pour plus d’informations, consultez [Utiliser des certificats avec votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Télécharger un disque dur virtuel

Copiez les images de disque à utiliser dans des objets blob de pages dans le compte de stockage local que vous avez créé précédemment. Vous pouvez utiliser un outil comme [AzCopy](../storage/common/storage-use-azcopy-v10.md) pour charger le disque dur virtuel (VHD) sur le compte de stockage. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Utilisez les commandes suivantes avec AzCopy 10 :  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Voici un exemple de sortie : 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>Créer un disque géré à partir du disque dur virtuel

Pour créer un disque managé à partir du disque dur virtuel chargé, exécutez la commande suivante :

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Voici un exemple de sortie : 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Voici un exemple de sortie. Pour plus d’informations sur cette applet de commande, consultez [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Créer une image de machine virtuelle à partir de l’image de disque managé

Pour créer une image de machine virtuelle à partir du disque managé, exécutez la commande suivante. Remplacez *\<Disk name>* , *\<OS type>* et *\<Disk size>* par des valeurs réelles.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Voici un exemple de sortie. Pour plus d’informations sur cette applet de commande, consultez [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>Créer votre machine virtuelle avec des ressources créées précédemment

Avant de créer et déployer la machine virtuelle, vous devez créer un réseau virtuel et lui associer une interface de réseau virtuel.

> [!IMPORTANT]
> Les règles suivantes s’appliquent :
> - Vous ne pouvez créer qu’un seul réseau virtuel, même dans plusieurs groupes de ressources. Le réseau virtuel doit avoir exactement le même espace d’adressage que le réseau logique.
> - Le réseau virtuel ne peut avoir qu’un seul sous-réseau. Le sous-réseau doit avoir exactement le même espace d’adressage que le réseau virtuel.
> - Lorsque vous créez la carte d’interface réseau virtuelle, vous ne pouvez utiliser que la méthode d’allocation statique. L’utilisateur doit fournir une adresse IP privée.

### <a name="query-the-automatically-created-virtual-network"></a>Interroger le réseau virtuel créé automatiquement

Lorsque vous activez le calcul à partir de l’interface utilisateur locale de votre appareil, un réseau virtuel `ASEVNET` est créé automatiquement sous le groupe de ressources `ASERG`. 

Utilisez la commande suivante pour interroger le réseau virtuel existant :

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Créer une carte d’interface réseau virtuelle

Pour créer une carte d’interface de réseau virtuel à l’aide de l’ID de sous-réseau du réseau virtuel, exécutez la commande suivante :

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Voici un exemple de sortie :

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Pendant que vous créez une carte d’interface de réseau virtuel pour une machine virtuelle, vous pouvez éventuellement transmettre l’IP publique. Dans ce cas, l’adresse IP publique retourne l’adresse IP privée. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Créer une machine virtuelle

Vous pouvez maintenant utiliser l’image de machine virtuelle pour créer une machine virtuelle et l’attacher au réseau virtuel que vous avez créé précédemment.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Une fois que vous avez créé et mis sous tension la machine virtuelle, vous allez utiliser le nom d’utilisateur et le mot de passe suivants pour vous y connecter.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Selon que vous avez créé une machine virtuelle Windows ou Linux, les instructions de connexion peuvent être différentes.

### <a name="connect-to-a-linux-vm"></a>Se connecter à une machine virtuelle Linux

Pour vous connecter à une machine virtuelle Linux, procédez comme suit :

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

  Si vous avez utilisé une adresse IP publique lors de la création de la machine virtuelle, vous pouvez l’utiliser pour vous connecter à la machine virtuelle. Pour obtenir l’adresse IP publique, exécutez la commande suivante : 

  ```powershell
  $publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
  ```
  Dans cet exemple, l’adresse IP publique est identique à l’adresse IP privée que vous avez transmise lors de la création de l’interface du réseau virtuel.

### <a name="connect-to-a-windows-vm"></a>Se connecter à une machine virtuelle Windows

Pour vous connecter à une machine virtuelle Windows, procédez comme suit :

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>Gérer la machine virtuelle

Les sections suivantes décrivent quelques-unes des opérations courantes que vous pouvez créer sur votre appareil Azure Stack Edge Pro.

### <a name="list-vms-that-are-running-on-the-device"></a>Lister les machines virtuelles en cours d’exécution sur l’appareil

Pour retourner une liste de toutes les machines virtuelles en cours d’exécution sur votre appareil Azure Stack Edge, exécutez la commande suivante :


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Activer la machine virtuelle

Pour activer une machine virtuelle en cours d’exécution sur votre appareil, exécutez l’applet de commande suivante :

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Pour plus d’informations sur cette applet de commande, consultez [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Interrompre ou arrêter la machine virtuelle

Pour arrêter une machine virtuelle en cours d’exécution sur votre appareil, exécutez l’applet de commande suivante :


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Pour plus d’informations sur cette applet de commande, consultez [Stop-AzureRmVM cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Ajouter un disque de données

Si les besoins en charge de travail sur votre machine virtuelle augmentent, vous devrez peut-être ajouter un disque de données. Pour cela, exécutez la commande suivante :

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Pour supprimer une machine virtuelle de votre appareil, exécutez l’applet de commande suivante :

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Pour plus d’informations sur cette applet de commande, consultez [Remove-AzureRmVm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Étapes suivantes

[Applets de commande Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)