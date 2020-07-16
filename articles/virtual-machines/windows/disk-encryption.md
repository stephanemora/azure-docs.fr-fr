---
title: Chiffrement côté serveur d’Azure Managed Disks - PowerShell
description: Le Stockage Azure protège vos données en les chiffrant au repos avant de les rendre persistantes dans des clusters de stockage. Vous pouvez compter sur des clés gérées par Microsoft pour le chiffrement de vos disques managés, ou bien utiliser des clés gérées par le client pour gérer le chiffrement avec vos propres clés.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: c3a73028350054d54c6714107bfdfa7ead3ee4a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610430"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Chiffrement côté serveur de disques managés Azure

Les disques managés Azure chiffrent automatiquement vos données par défaut lors de leur conservation dans le cloud. Le chiffrement côté serveur protège vos données et vous aide à honorer les engagements de votre entreprise en matière de sécurité et de conformité.

Les données dans les disque managés Azure sont chiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Pour plus d’informations sur les modules cryptographiques des disque managés Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Le chiffrement n’a pas d’impact sur les performances des disques managés et n’entraîne aucun coût supplémentaire. 

> [!NOTE]
> Les disques temporaires ne sont pas des disques managés et ne sont pas chiffrés par chiffrement côté serveur. Pour plus d’informations sur les disques temporaires, consultez [Vue d’ensemble des disques managés : rôles de disque](managed-disks-overview.md#disk-roles).

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Vous pouvez vous appuyer sur les clés gérées par la plateforme pour le chiffrement de votre disque managé, ou vous pouvez gérer le chiffrement en utilisant vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, vous pouvez spécifier une *clé gérée par le client* à utiliser pour le chiffrement et le déchiffrement de toutes les données dans les disques managés. 

Les sections suivantes décrivent en détail chacune des options de gestion des clés.

## <a name="platform-managed-keys"></a>Clés gérées par la plateforme

Par défaut, les disques managés utilisent des clés de chiffrement gérées par la plateforme. À compter du 10 juin 2017, l’ensemble des nouveaux disques managés, des instantanés et des images ainsi que les nouvelles données écrites sur des disques managés existants sont automatiquement chiffrés au repos avec des clés gérées par les plateformes.

## <a name="customer-managed-keys"></a>Clés gérées par le client

Vous pouvez choisir de gérer le chiffrement au niveau de chaque disque managé, avec vos propres clés. Le chiffrement côté serveur pour les disques managés avec des clés gérées par le client offre une expérience intégrée avec Azure Key Vault. Vous pouvez importer [vos clés RSA](../../key-vault/keys/hsm-protected-keys.md) vers votre Key Vault ou générer de nouvelles clés RSA dans Azure Key Vault. 

Les disques managés Azure gèrent le chiffrement et le déchiffrement de manière entièrement transparente à l’aide du [chiffrement d’enveloppe](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ils chiffrent les données à l’aide d’une clé de chiffrement de données [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, qui est à son tour protégée à l’aide de vos clés. Le service Stockage génère des clés de chiffrement de données et les chiffre avec des clés gérées par le client à l’aide du chiffrement RSA. Le chiffrement d’enveloppe vous permet de faire pivoter (modifier) régulièrement vos clés en fonction de vos stratégies de conformité sans perturber le fonctionnement de vos machines virtuelles. Lorsque vous faites pivoter vos clés, le service Stockage rechiffre les clés de chiffrement de données avec les nouvelles clés gérées par le client. 

Vous devez accorder l’accès aux disques managés dans votre Key Vault pour utiliser vos clés pour le chiffrement et le déchiffrement de la clé de chiffrement. Cela vous permet de contrôler intégralement vos données et vos clés. Vous pouvez désactiver vos clés ou révoquer l’accès aux disques managés à tout moment. Vous pouvez également auditer l’utilisation de la clé de chiffrement avec la surveillance Azure Key Vault pour vous assurer que seuls les disques managés ou d’autres services Azure approuvés accèdent à vos clés.

Pour les SSD Premium, les SSD Standard et les HDD Standard : si vous désactivez ou supprimez votre clé, toutes les machines virtuelles comportant des disques qui utilisent cette clé s’arrêtent automatiquement. Elles ne seront plus utilisables tant que la clé n’aura pas été réactivée ou que vous n’aurez pas affecté une nouvelle clé.

Pour les Disques Ultra : si vous désactivez ou supprimez une clé, les machines virtuelles comportant des Disques Ultra qui utilisent cette clé ne s’arrêtent pas automatiquement. Une fois que vous aurez libéré et redémarré les machines virtuelles, les disques arrêteront d’utiliser la clé et les machines virtuelles ne sont plus en ligne. Pour les remettre en ligne, vous devrez affecter une nouvelle clé ou activer la clé existante.

Le diagramme suivant montre comment les disques managés utilisent Azure Active Directory et Azure Key Vault pour effectuer des demandes en utilisant la clé gérée par le client :

![Workflow de disques managés et de clés gérées par le client. Un administrateur crée un Azure Key Vault, puis crée et configure un jeu de chiffrement de disque. Le jeu est associé à une machine virtuelle qui permet au disque d’utiliser Azure AD pour s’authentifier](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


La liste suivante décrit le diagramme plus en détail :

1. Un administrateur Azure Key Vault crée des ressources de coffre de clés.
1. L’administrateur du coffre de clés importe ses clés RSA vers Key Vault ou génère de nouvelles clés RSA dans Key Vault.
1. Cet administrateur crée une instance de ressource de jeu de chiffrement de disque, en spécifiant un ID Azure Key Vault et une URL de clé. Le jeu de chiffrement de disque est une nouvelle ressource introduite pour simplifier la gestion des clés pour les disques managés. 
1. Lors de la création d’un jeu de chiffrement de disque, une [Identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md) est créée dans Azure Active Directory (AD) et est associée au jeu de chiffrement de disque. 
1. L’administrateur du coffre de clés Azure accorde ensuite à l’identité managée l’autorisation d’effectuer des opérations dans le coffre de clés.
1. Un utilisateur de machine virtuelle crée des disques en les associant au jeu de chiffrement de disque. L’utilisateur de la machine virtuelle peut également activer le chiffrement côté serveur avec des clés gérées par le client pour les ressources existantes en les associant au jeu de chiffrement de disque. 
1. Les disques managés utilisent l’identité managée pour envoyer des demandes au Azure Key Vault.
1. Pour la lecture ou l’écriture de données, les disques managés envoient des demandes à Azure Key Vault pour chiffrer (envelopper) et déchiffrer (désenvelopper) la clé de chiffrement des données, afin d’effectuer le chiffrement et le déchiffrement des données. 

Pour révoquer l’accès aux clés managées par le client, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

### <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrictions

Pour le moment, les clés gérées par le client sont soumises aux restrictions suivantes :

- Si cette fonctionnalité est activée pour votre disque, vous ne pouvez pas la désactiver.
    Pour contourner le problème, vous devez [copier toutes les données](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) sur un autre disque managé qui n’utilise pas de clés gérées par le client.
- Seules les [clés RSA HSM et de logiciels](../../key-vault/keys/about-keys.md) d’une taille de 2080 sont prises en charge ; aucune autre clé ou taille n’est prise en charge.
- Les disques créés à partir d’images personnalisées et chiffrées à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés à l’aide des mêmes clés gérées par le client et doivent figurer dans le même abonnement.
- Les instantanés créés à partir de disques chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés avec les mêmes clés gérées par le client.
- Toutes les ressources liées à vos clés gérées par le client (coffres de clés Azure, jeux de chiffrement de disque, machines virtuelles, disques et instantanés) doivent se trouver dans le même abonnement et la même région.
- Les disques, les instantanés et les images chiffrées avec des clés gérées par le client ne peuvent pas être déplacés vers un autre abonnement.
- Les disques managés chiffrés à l’aide du chiffrement côté serveur et avec des clés gérées par le client ne peuvent pas être également chiffrés avec Azure Disk Encryption et vice versa.
- Pour plus d’informations sur l’utilisation de clés gérées par le client avec des galeries d’images partagées, consultez [Préversion : Utiliser des clés gérées par le client pour le chiffrement d’images](../image-version-encryption.md).

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuration de d’Azure Key Vault et de DiskEncryptionSet

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Créer une machine virtuelle à l’aide d’une image marketplace, en chiffrant le système d’exploitation et les disques de données avec des clés gérées par le client

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Créer un disque vide chiffré à l’aide du chiffrement côté serveur avec des clés gérées par le client et l’attacher à une machine virtuelle

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>Chiffrer des disques managés existants 

Vos disques existants ne doivent pas être attachés à une machine virtuelle en cours d’exécution pour que vous puissiez les chiffrer à l’aide du script suivant :

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Créer un groupe de machines virtuelles identiques en utilisant une image de la Place de marché, en chiffrant le système d’exploitation et les disques de données avec des clés gérées par le client

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Modifier la clé d’un DiskEncryptionSet pour faire pivoter la clé de toutes les ressources référençant le DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Rechercher l’état de chiffrement côté serveur d’un disque

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques gérés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques gérés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Chiffrement côté serveur et chiffrement de disque Azure

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utilise la fonctionnalité [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows pour chiffrer des disques managés avec des clés gérées par le client au sein de la machine virtuelle invitée.  Le chiffrement côté serveur avec des clés gérées par le client améliore l’utilisation de Azure Disk Encryption en vous permettant d’utiliser des types et des images de système d’exploitation pour vos machines virtuelles en chiffrant les données dans le service de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez les modèles Azure Resource Manager permettant de créer des disques chiffrés avec des clés gérées par le client](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/general/overview.md)
- [Répliquer des machines avec des disques activés par les clés gérées par le client](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurer la reprise d’activité des machines virtuelles VMware sur Azure avec PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurer la récupération d’urgence dans Azure pour les machines virtuelles Hyper-V à l’aide de PowerShell et d’Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
