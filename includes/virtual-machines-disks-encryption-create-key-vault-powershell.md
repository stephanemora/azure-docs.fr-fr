---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9ad82e65258dd985ce351b5fa11156ccdd2ef977
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771990"
---
1. Vérifiez que vous avez installé la [version d’Azure PowerShell](/powershell/azure/install-az-ps) la plus récente et que vous êtes connecté à un compte Azure avec Connect-AzAccount.

1. Créer une instance Azure Key Vault et une clé de chiffrement.

    Quand vous créez l’instance Key Vault, vous devez activer la protection de purge. La protection de purge garantit qu’une clé supprimée ne peut pas être supprimée définitivement tant que la période de rétention n’est pas écoulée. Ces paramètres vous protègent contre la perte de données en raison d’une suppression accidentelle. Ces paramètres sont obligatoires lors de l’utilisation d’un coffre de clés Key Vault pour le chiffrement des disques managés.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName `
    -ResourceGroupName $ResourceGroupName `
    -Location $LocationName `
    -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName `
          -Name $keyName `
          -Destination $keyDestination 
    ```

1.    Créer une instance de DiskEncryptionSet. Vous pouvez affecter à RotationToLatestKeyVersionEnabled la valeur $true pour activer la rotation automatique de la clé. Lorsque vous activez la rotation automatique, le système met automatiquement à jour tous les disques managés, les captures instantanées et les images référençant le jeu de chiffrement de disque pour utiliser la nouvelle version de la clé dans un délai d’une heure.  
    
        ```powershell
      $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName `
            -SourceVaultId $keyVault.ResourceId `
            -KeyUrl $key.Key.Kid `
            -IdentityType SystemAssigned `
            -RotationToLatestKeyVersionEnabled $false

       $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName `
               -ResourceGroupName $ResourceGroupName `
               -InputObject $desConfig
        ```

1.    Accorder à la ressource DiskEncryptionSet l’accès au coffre de clés.

        > [!NOTE]
        > La création de l’identité de votre DiskEncryptionSet dans votre annuaire Azure Active Directory peut prendre quelques minutes. Si vous recevez une erreur comme « Impossible de trouver l’objet Active Directory » lors de l’exécution de la commande suivante, attendez quelques minutes et réessayez.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```
