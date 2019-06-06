---
title: Chiffrer des disques dans des groupes identiques Azure avec Azure PowerShell | Microsoft Docs
description: Découvrez comment utiliser Azure PowerShell pour chiffrer les instances de machine virtuelle et les disques attachés dans un groupe de machines virtuelles identiques Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: a582a4787a4b215d82dcbff60be8853793f92c32
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728368"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Chiffrer le système d’exploitation et disques de données associés dans un machine virtuelle identique avec Azure PowerShell

Conçue pour protéger les données au repos, Azure Disk Encryption (ADE) est une technologie de chiffrement standard prise en charge par les groupes de machines virtuelles identiques. Le chiffrement peut être activé pour les groupes de machines virtuelles identiques Windows et Linux. Pour plus d’informations, consultez [Azure Disk Encryption pour machines virtuelles Windows et Linux](../security/azure-security-disk-encryption.md).

Azure Disk Encryption est pris en charge :
- Pour les groupes identiques créés avec des disques managés, et non pris en charge pour les groupes identiques de disques natifs (ou non managés).
- Pour les volumes de données et de systèmes d’exploitation dans les groupes identiques Windows. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
- Pour les volumes de données dans les groupes identiques Linux. Chiffrement de disque de système d’exploitation n’est pas pris en charge à l’heure actuelle pour les jeux de mise à l’échelle Linux.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Créer un coffre de clés Azure activé pour le chiffrement de disque

Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation.

Créez un coffre de clés avec [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Pour autoriser l’utilisation du coffre de clés pour le chiffrement de disque, définissez le paramètre *EnabledForDiskEncryption*. L’exemple suivant définit également les variables pour le nom du groupe de ressources, le nom du coffre de clés et l’emplacement. Indiquez un nom unique pour votre coffre de clés :

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Utiliser un coffre de clés existant

Cette étape est requise uniquement si vous souhaitez utiliser un coffre de clés existant avec le chiffrement de disque. Ignorez cette étape si vous avez créé un coffre de clés dans la section précédente.

Vous pouvez activer un coffre de clés existant dans le même abonnement et la même région que le groupe identique pour le chiffrement de disque avec [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Définissez le nom du coffre de clés existant dans la variable *$vaultName*, comme ceci :


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Créer un groupe identique

Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour les instances de machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

À présent, créez un groupe de machines virtuelles identiques avec [New-AzVmss](/powershell/module/az.compute/new-azvmss). Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985 :

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Activer le chiffrement

Pour chiffrer des instances de machine virtuelle dans un groupe identique, vous devez d’abord obtenir certaines informations sur l’URI et l’ID de ressources du coffre de clés avec [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Les variables obtenues sont ensuite utilisées pour démarrer le processus de chiffrement avec [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension) :


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Quand vous y êtes invité, tapez *y* pour continuer le processus de chiffrement de disque sur les instances de machine virtuelle dans le groupe identique.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Activer le chiffrement à l’aide de la KEK à encapsuler la clé

Vous pouvez également utiliser une clé de chiffrement pour renforcer la sécurité lors du chiffrement de l’ensemble d’échelle de machine virtuelle.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  La syntaxe de la valeur du paramètre de disque-encryption-Key Vault est la chaîne d’identificateur complet :</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> La syntaxe de la valeur du paramètre de clé de chiffrement est l’URI complet de la clé KEK comme dans :</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Vérifier la progression du chiffrement

Pour vérifier l’état du chiffrement de disque, utilisez [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption) :


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Une fois que les instances de machine virtuelle sont chiffrées, la variable *EncryptionSummary* retourne *ProvisioningState/succeeded* comme dans l’exemple de sortie suivant :

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Désactiver le chiffrement

Si vous ne souhaitez plus utiliser les disques d’instances de machine virtuelle chiffrés, désactivez le chiffrement avec [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption), comme ceci :


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Étapes suivantes

- Dans cet article, vous avez utilisé Azure PowerShell pour chiffrer un groupe de machines virtuelles identiques. Vous pouvez également utiliser [Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md) ou des modèles pour [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Si vous souhaitez avoir Azure Disk Encryption appliqué après l’approvisionnée d’une autre extension, vous pouvez utiliser [séquencement d’extensions](virtual-machine-scale-sets-extension-sequencing.md). Vous pouvez utiliser [ces exemples](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) pour commencer.
