---
title: Utiliser PowerShell pour configurer des clés gérées par le client
titleSuffix: Azure Storage
description: Découvrez comment utiliser PowerShell afin de configurer des clés gérées par le client pour le chiffrement du stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a3fdde755a5e024efead5c8861a1d5cd769b6d23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036826"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configurer des clés gérées par le client avec Azure Key Vault à l’aide de PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés Azure avec des clés gérées par le client à l’aide de PowerShell. Pour en savoir plus sur la création d’un coffre de clés à l’aide d’Azure CLI, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide de PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité au compte de stockage

Pour activer des clés gérées par le client pour votre compte de stockage, affectez tout d’abord à votre compte de stockage une identité managée affectée au système. Cette identité managée vous sera utile pour autoriser le compte de stockage à accéder au coffre de clés.

Pour affecter une identité managée via PowerShell, appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Pour plus d’informations sur la configuration d’identités managées affectées à un système, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Pour créer un coffre de clés à l’aide de PowerShell, installez la version 2.0.0 ou ultérieure du module PowerShell [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0). Appelez ensuite [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) pour créer un coffre de clés.

Le coffre de clés que vous utilisez pour stocker des clés gérées par le client pour le chiffrement du stockage Azure doit disposer de deux paramètres de protection de clés, **Suppression réversible** et **Ne pas vider**. Dans les versions 2.0.0 et ultérieures du module Az.KeyVault, la suppression réversible est activée par défaut lorsque vous créez un coffre de clés.

L’exemple suivant crée un coffre de clés avec les propriétés **Suppression réversible** et **Ne pas vider** activées. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Pour savoir comment activer les options **Suppression réversible** et les **Ne pas vider** sur un coffre de clés existant avec PowerShell, consultez les sections intitulées **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans [Guide pratique pour utiliser la suppression réversible avec Azure Power​Shell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès au coffre de clés

Configurez ensuite la stratégie d’accès au coffre de clés de sorte que le compte de stockage dispose des autorisations nécessaires pour y accéder. À cette étape, vous utiliserez l’identité managée que vous avez précédemment affectée au compte de stockage.

Pour définir la stratégie d’accès du coffre de clés, appelez la commande [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Créer une clé

Créez ensuite une clé dans le coffre de clés. Pour créer une clé, appelez la commande [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Le chiffrement du stockage Azure prend en charge les clés RSA et RSA-HSM dans les tailles 2048, 3072 et 4096. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec les clés gérées par le client

Par défaut, le chiffrement du stockage Azure utilise des clés gérées par Microsoft. Dans cette étape, configurez votre compte de stockage Azure pour utiliser des clés gérées par le client à l’aide d’Azure Key Vault, puis spécifiez la clé à lui associer.

Lorsque vous configurez le chiffrement avec des clés gérées par le client, vous pouvez choisir de faire pivoter automatiquement la clé utilisée pour le chiffrement lorsque la version change dans le coffre de clés associé. Vous pouvez également spécifier explicitement une version de clé à utiliser pour le chiffrement jusqu’à ce que la version de clé soit mise à jour manuellement.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Configurer le chiffrement pour la rotation automatique des clés gérées par le client

Afin de configurer le chiffrement avec permutation automatique des clés gérées par le client, installez le module [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), version 2.0.0 ou ultérieure.

Pour faire pivoter automatiquement les clés gérées par le client, omettez la version de clé lorsque vous configurez des clés gérées par le client pour le compte de stockage. Appelez [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage, comme indiqué dans l’exemple suivant, et incluez l’option **-KeyvaultEncryption** pour activer les clés gérées par le client pour le compte de stockage. N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Configurer le chiffrement pour la rotation manuelle des versions de clé

Pour spécifier explicitement une version de clé à utiliser pour le chiffrement, fournissez la version de clé lorsque vous configurez le chiffrement avec les clés gérées par le client pour le compte de stockage. Appelez [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage, comme indiqué dans l’exemple suivant, et incluez l’option **-KeyvaultEncryption** pour activer les clés gérées par le client pour le compte de stockage. N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Lorsque vous faites pivoter manuellement la version de clé, vous devez mettre à jour les paramètres de chiffrement du compte de stockage afin d’utiliser la nouvelle version. Tout d’abord, appelez la commande [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) pour obtenir la dernière version de la clé. Appelez ensuite la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage afin d’utiliser la nouvelle version de la clé, comme indiqué dans l’exemple précédent.

## <a name="use-a-different-key"></a>Utiliser une autre clé

Pour modifier la clé utilisée pour le chiffrement de Stockage Azure, appelez la cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) comme indiqué dans [Configurer des clés de chiffrement gérées par le client](#configure-encryption-with-customer-managed-keys) et indiquez le nom et la version de la nouvelle clé. Si la nouvelle clé se trouve dans un coffre de clés différent, mettez également à jour l’URI du coffre de clés.

## <a name="revoke-customer-managed-keys"></a>Révoquer des clés gérées par le client

Vous pouvez révoquer des clés gérées par le client en supprimant la stratégie d’accès du coffre de clés. Pour révoquer une clé gérée par le client, appelez la commande [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy), comme indiqué dans l’exemple suivant. N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Désactiver les clés gérées par le client

Quand vous désactivez les clés gérées par le client, votre compte de stockage est de nouveau chiffré avec des clés gérées par Microsoft. Pour désactiver les clés gérées par le client, appelez la cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) avec l’option `-StorageEncryption`, comme indiqué dans l’exemple suivant. N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
