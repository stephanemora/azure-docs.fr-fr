---
title: Comment mettre à niveau Azure Disk Encryption sur un disque
description: Cet article fournit des instructions sur la mise à niveau d’Azure Disk Encryption sur un disque.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 05/27/2021
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0cdc32e303de7d9a26a99d1f08188dc54539930b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758126"
---
# <a name="upgrading-the-azure-disk-encryption-version"></a>Mise à niveau de la version d’Azure Disk Encryption

La première version d’Azure Disk Encryption (ADE) s’appuyait sur Azure Active Directory (AAD) pour l’authentification. Ce n’est pas le cas de la version actuelle.  Nous recommandons vivement l’utilisation de la version la plus récente.

## <a name="determine-ade-version"></a>Déterminer la version d’ADE

Les versions d’ADE dans l’étendue de la migration sont les suivantes :
- **Windows** : 1.1.* (ADE sur la machine virtuelle doit être mis à niveau vers la version 2.2)
- **Linux** : 0.1.* (ADE sur la machine virtuelle doit être mis à niveau vers la version 1.2)

Vous pouvez déterminer la version d’ADE avec laquelle une machine virtuelle a été chiffrée via Azure CLI, Azure PowerShell ou le portail Azure.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

Pour déterminer la version d’ADE, exécutez la commande Azure CLI [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view).

```azurecli-interactive
az vm get-instance-view --resource-group  <ResourceGroupName> --name <VMName> 
```

Localisez l’extension AzureDiskEncryption dans la sortie et identifiez le numéro de version mentionné dans le champ « TypeHandlerVersion » dans la sortie.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pour déterminer la version d’ADE, exécutez la commande Azure PowerShell [Get-AzVM](/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
Get-AzVM -ResourceGroupName <ResourceGroupName> -Name <VMName> -Status
```

Localisez l’extension AzureDiskEncryption dans la sortie et identifiez le numéro de version mentionné dans le champ « TypeHandlerVersion » dans la sortie.

# <a name="portal"></a>[Portail](#tab/Portal)

Accédez au panneau « Extensions » de votre machine virtuelle dans le portail Azure.

:::image type="content" source="../media/disk-encryption/ade-version-1.png" alt-text="Capture d’écran du portail de la version ADE 1":::

Choisissez l’extension « AzureDiskEncryption » pour Windows, ou « AzureDiskEncryptionForLinux » pour Linux, puis recherchez le numéro de version dans le champ « Version ».

:::image type="content" source="../media/disk-encryption/ade-version-2.png" alt-text="Capture d’écran du portail de la version ADE 2":::

---

## <a name="how-to-migrate"></a>Migration

La migration à partir d’Azure Disk Encryption (avec AAD) vers Azure Disk Encryption (sans AAD) n’est disponible que via Azure PowerShell. Assurez-vous que vous disposez de la dernière version d’Azure PowerShell et au minimum du [module Azure PowerShell Az version 5.9.0](/powershell/azure/new-azureps-module-az) installé.

Pour effectuer une mise à niveau d’Azure Disk Encryption (avec AAD) vers Azure Disk Encryption (sans AAD), utilisez la cmdlet PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

> [!WARNING]
> La cmdlet Set-AzVMDiskEncryptionExtension doit être utilisée uniquement sur des machines virtuelles chiffrées avec Azure Disk Encryption (avec AAD). Toute tentative de migration d’une machine virtuelle non chiffrée ou d’une machine virtuelle chiffrée avec Azure Disk Encryption (sans AAD) entraîne une erreur de terminal.

```azurepowershell-interactive
Set-AzVMDiskEncryptionExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Migrate
```

Lorsque la cmdlet vous invite à confirmer, entrez « Y ».  La version d’ADE sera mise à jour et la machine virtuelle redémarrée. La sortie ressemble à l'exemple suivant :

```bash
> Set-AzVMDiskEncryptionExtension -ResourceGroupName myResourceGroup -VMName myVM -Migrate

Update AzureDiskEncryption version?
This cmdlet updates Azure Disk Encryption version to single pass (Azure Disk Encryption without AAD). This may reboot
the machine and takes 10-15 minutes to finish. Are you sure you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
Azure Disk Encryption Extension Public Settings
"KeyVaultResourceId": /subscriptions/ea500758-3163-4849-bd2c-3e50f06efa7a/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault
"SequenceVersion":
"MigrateFlag": Migrate
"KeyVaultURL": https://myKeyVault.vault.azure.net/
"AADClientID": d29edf8c-3fcb-42e7-8410-9e39fdf0dd70
"KeyEncryptionKeyURL":
"KekVaultResourceId":
"EncryptionOperation": EnableEncryption
"AADClientCertThumbprint":
"VolumeType":
"KeyEncryptionAlgorithm":

Running ADE extension (with AAD) for -Migrate..
ADE extension (with AAD) is now complete. Updating VM model..
Running ADE extension (without AAD) for -Migrate..
ADE extension (without AAD) is now complete. Clearing VM model..

RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


> [!IMPORTANT]
> La mise à niveau prendra au moins 10 à 15 minutes. N’annulez pas la cmdlet pendant la mise à niveau. Cela met en danger l’intégrité de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes liés à Azure Disk Encryption](disk-encryption-troubleshooting.md)
