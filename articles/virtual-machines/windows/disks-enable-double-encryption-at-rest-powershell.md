---
title: Azure PowerShell - Activer le double chiffrement au repos - disques managés
description: Activez le double chiffrement au repos pour vos disques managés à l’aide d’Azure PowerShell.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0f386e4ba4a1835b88b753574bde23e93f7f8d17
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235636"
---
# <a name="azure-powershell---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure PowerShell - Activer le double chiffrement au repos sur vos disques managés

Le Stockage sur disque Azure prend en charge le double chiffrement au repos pour les disques managés. Pour obtenir des informations conceptuelles sur le double chiffrement au repos, ainsi que d’autres types de chiffrement de disque managé, consultez la section [Double chiffrement au repos](disk-encryption.md#double-encryption-at-rest) de notre article sur le chiffrement de disque.

## <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Prérequis

Installez la dernière [version d’Azure PowerShell](/powershell/azure/install-az-ps) et connectez-vous à un compte Azure à l’aide de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-4.3.0).

## <a name="getting-started"></a>Prise en main

1. Créer une instance Azure Key Vault et une clé de chiffrement.

    Lorsque vous créez l’instance Key Vault, vous devez activer la suppression réversible et la protection de purge. La suppression réversible permet de s’assurer que Key Vault contient une clé supprimée pour une période de rétention donnée (90 jours par défaut). La protection de purge garantit qu’une clé supprimée ne peut pas être supprimée définitivement tant que la période de rétention n’est pas écoulée. Ces paramètres vous protègent contre la perte de données en raison d’une suppression accidentelle. Ces paramètres sont obligatoires lors de l’utilisation d’un coffre de clés Key Vault pour le chiffrement des disques managés.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Créez un DiskEncryptionSet avec encryptionType défini sur EncryptionAtRestWithPlatformAndCustomerKeys. Utilisez l’API version **2020-05-01** dans le modèle Azure Resource Manager (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName "yourDESForDoubleEncryption" `
    -keyVaultId "subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.KeyVault/vaults/yourKeyVaultName" `
    -keyVaultKeyUrl "https://yourKeyVaultName.vault.azure.net/keys/yourKeyName/403445136dee4a57af7068cab08f7d42" `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region "CentralUSEUAP"
    ```

1. Accorder à la ressource DiskEncryptionSet l’accès au coffre de clés.

    > [!NOTE]
    > La création de l’identité de votre DiskEncryptionSet dans votre annuaire Azure Active Directory peut prendre quelques minutes. Si vous recevez une erreur comme « Impossible de trouver l’objet Active Directory » lors de l’exécution de la commande suivante, attendez quelques minutes et réessayez.

    ```powershell  
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et configuré ces ressources, vous pouvez les utiliser pour sécuriser vos disques managés. Les liens suivants contiennent des exemples de scripts, chacun avec un scénario respectif, que vous pouvez utiliser pour sécuriser vos disques managés.

[Azure PowerShell - Activer les clés gérées par le client avec le chiffrement côté serveur - disques managés](disks-enable-customer-managed-keys-powershell.md)
[Exemples de modèles Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)