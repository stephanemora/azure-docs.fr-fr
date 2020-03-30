---
title: Chiffrer des disques sur une machine virtuelle Windows dans Azure
description: Chiffrer des disques virtuels sur une machine virtuelle Windows pour renforcer la sécurité avec Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033526"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Chiffrer des disques virtuels sur une machine virtuelle Windows
Pour renforcer la sécurité et la conformité de la machine virtuelle, les disques virtuels dans Azure peuvent être chiffrés. Les disques sont chiffrés à l’aide de clés de chiffrement sécurisées dans un coffre Azure Key Vault. Vous contrôlez ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une machine virtuelle Windows avec Azure PowerShell. Vous pouvez également [chiffrer des machines virtuelles Linux](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Présentation du chiffrement de disque
Les disques virtuels des machines virtuelles Windows sont chiffrés au repos avec BitLocker. Le chiffrement de disques virtuels dans Azure n’occasionne pas de frais. Les clés de chiffrement sont stockées dans un coffre Azure Key Vault avec une protection logiciel ; cependant, vous pouvez les importer ou les générer dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels attachés à une machine virtuelle. Vous en gardez le contrôle et pouvez effectuer un audit de leur utilisation. 

Le processus de chiffrement d’une machine virtuelle est le suivant :

1. Créez une clé de chiffrement dans un coffre de clés Azure.
1. Configurez la clé de chiffrement afin de la rendre utilisable pour le chiffrement des disques.
1. Activez le chiffrement de vos disques virtuels.
1. Les clés de chiffrement requises sont demandées à Azure Key Vault.
1. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.


## <a name="requirements-and-limitations"></a>Exigences et limitations

Configuration requise et scénarios pris en charge pour le chiffrement de disque :

* Activation du chiffrement sur les nouvelles machines virtuelles Windows à partir d’images de la Place de marché Azure ou d’images de disque dur virtuel personnalisées.
* Activation du chiffrement sur les machines virtuelles Windows existantes dans Azure.
* Activation du chiffrement sur les machines virtuelles Windows configurées avec des espaces de stockage.
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles Windows.
* Machines virtuelles de niveau standard (A, D, DS, G, GS et GS, par exemple).

    > [!NOTE]
    > Toutes les ressources (notamment le coffre de clés, le compte de stockage et la machine virtuelle) doivent appartenir à la même région et au même abonnement Azure.

Le chiffrement de disque n’est à l’heure actuelle pas pris en charge dans les scénarios suivants :

* Machines virtuelles de niveau de base.
* Machines virtuelles créées suivant le modèle de déploiement Classic.
* Mise à jour des clés de chiffrement sur une machine virtuelle déjà chiffrée.
* Intégration au Service de gestion de clés local.


## <a name="create-an-azure-key-vault-and-keys"></a>Créer un coffre Azure Key Vault et des clés
Avant de commencer, vérifiez que la dernière version du module Azure PowerShell est installée. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview). Dans les exemples de commandes suivants, remplacez tous les exemples de paramètres par vos propres valeurs (nom, emplacement et clé : *myResourceGroup*, *myKeyVault*, *myVM*, etc.).

La première étape consiste à créer un coffre de clés Azure pour y stocker les clés de chiffrement. Les coffres Azure Key Vault peuvent stocker des clés, des secrets et des mots de passe, ce qui permet de les implémenter en toute sécurité dans des applications et des services. Dans le cas du chiffrement de disque virtuel, il s’agit de créer un coffre de clés pour stocker une clé de chiffrement servant à chiffrer ou à déchiffrer les disques virtuels. 

Activez le fournisseur Azure Key Vault au sein de votre abonnement Azure avec [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), puis créez un groupe de ressources avec [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *USA Est* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Le coffre Azure Key Vault contenant les clés de chiffrement et les ressources de calcul associées, comme le stockage et la machine virtuelle proprement dite, doivent tous se trouver dans la même région. Créez un coffre Azure Key Vault avec [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) et activez-le pour une utilisation avec le chiffrement de disque. Spécifiez un nom de Key Vault unique pour *keyVaultName* comme suit :

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Pour stocker des clés de chiffrement, vous pouvez utiliser la protection logiciel ou HSM (module de sécurité matériel).  Un coffre de clés standard ne stocke que des clés protégées par logiciel. L’utilisation d’une protection HSM implique un coffre de clés Premium, qui suppose un coût supplémentaire. Pour créer un coffre Premium, ajoutez le paramètre *-Sku "Premium"* à l’étape précédente. L’exemple suivant utilise des clés protégées par logiciel puisque nous avons créé un coffre de clés standard. 

Pour les deux modèles de protection, la plateforme Azure doit être autorisée à demander les clés de chiffrement lorsque la machine virtuelle démarre pour déchiffrer les disques virtuels. Créez une clé de chiffrement dans le coffre Key Vault avec [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). L’exemple qui suit permet de créer une clé nommée *myKey* :

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Pour tester le processus de chiffrement, créez une machine virtuelle à l'aide de la commande [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L’exemple qui suit permet de créer une machine virtuelle nommée *myVM* en utilisant une image *Windows Server 2016 Datacenter*. Lorsque vous êtes invité à fournir vos informations d’identification, entrez un nom d’utilisateur et un mot de passe à utiliser pour votre machine virtuelle :

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Chiffrer une machine virtuelle
Chiffrez votre machine virtuelle avec [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) à l'aide de la clé Azure Key Vault. L’exemple qui suit permet de récupérer toutes les informations sur la clé, puis de chiffrer la machine virtuelle nommée *myVM* :

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Acceptez l’invite pour poursuivre le chiffrement de la machine virtuelle. La machine virtuelle redémarre pendant le processus. Une fois le processus de chiffrement terminé et la machine virtuelle redémarrée, vérifiez l'état du chiffrement avec [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) :

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Le résultat ressemble à l’exemple suivant :

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la gestion de coffres Azure Key Vault, voir [Configurer un coffre Key Vault pour des machines virtuelles](key-vault-setup.md).
* Pour plus d’informations sur le chiffrement de disque, notamment la préparation d’une machine virtuelle personnalisée chiffrée à télécharger vers Azure, consultez [Chiffrement de disque Azure](../../security/fundamentals/encryption-overview.md).
