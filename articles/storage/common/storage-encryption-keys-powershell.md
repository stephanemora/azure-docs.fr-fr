---
title: Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir de PowerShell
description: Découvrez comment utiliser PowerShell pour configurer des clés gérées par le client pour le chiffrement de stockage Azure. Clés gérées par le client permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: be876b370cd476bee2af7d90a9f0433fd80de3b4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233683"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir de PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés avec des clés gérées par le client à l’aide de PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité pour le compte de stockage

Pour activer les clés gérées par le client pour votre compte de stockage, vous devez tout d’abord affecter une identité gérée attribué par le système au compte de stockage. Vous utiliserez cette identité gérée pour accorder les autorisations de compte de stockage pour accéder au coffre de clés.

Pour attribuer une identité gérée à l’aide de PowerShell, appelez [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Pour plus d’informations sur la configuration attribué par le système d’identités gérées avec PowerShell, consultez [configurer managed identités pour les ressources Azure sur une machine virtuelle Azure à l’aide de PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Pour créer un coffre de clés à l’aide de PowerShell, appelez [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Le coffre de clés que vous utilisez pour stocker les clés gérées par le client pour le chiffrement du stockage Azure doit avoir deux paramètres de protection de clé est activées, **suppression réversible** et **ne pas vider**. 

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès de coffre de clés

Ensuite, configurez la stratégie d’accès pour le coffre de clés afin que le compte de stockage dispose des autorisations pour y accéder. Dans cette étape, vous allez utiliser l’identité gérée que vous avez précédemment affecté au compte de stockage.

Pour définir la stratégie d’accès pour le coffre de clés, appelez [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs pour utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Créer une clé

Ensuite, créez une nouvelle clé dans le coffre de clés. Pour créer une nouvelle clé, appelez [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs pour utiliser les variables définies dans les exemples précédents.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec des clés gérées par le client

Par défaut, le chiffrement du stockage Azure utilise des clés gérées par Microsoft. Dans cette étape, configurez votre compte de stockage Azure pour utiliser des clés gérées par le client et spécifier la clé à associer au compte de stockage.

Appelez [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs pour utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Mise à jour la version de la clé

Lorsque vous créez une nouvelle version d’une clé, vous devez mettre à jour le compte de stockage pour utiliser la nouvelle version. Tout d’abord, appelez [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) pour obtenir la dernière version de la clé. Appelez ensuite [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage pour utiliser la nouvelle version de la clé, comme indiqué dans la section précédente.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md) 
- [Qu’est Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
