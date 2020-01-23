---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021006"
---
Azure Data Explorer chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données. Les clés gérées par le client doivent être stockées dans [Azure Key Vault](/azure/key-vault/key-vault-overview). Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser une API d’Azure Key Vault pour générer des clés. Le cluster Azure Data Explorer et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour obtenir une explication détaillée sur les clés gérées par le client, consultez [Clés gérées par le client avec Azure Key Vault](/azure/storage/common/storage-service-encryption). Cet article vous montre comment configurer des clés gérées par le client.

> [!Note]
> Pour configurer des clés gérées par le client avec Azure Data Explorer, vous devez [définir deux propriétés sur le coffre de clés](/azure/key-vault/key-vault-ovw-soft-delete) : **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, utilisez [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) ou [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli). Seules les clés RSA et la taille de clé 2048 sont prises en charge.

## <a name="assign-an-identity-to-the-cluster"></a>Attribuer une identité au cluster

Pour activer des clés gérées par le client pour votre cluster, attribuez tout d’abord au cluster une identité managée affectée au système. Cette identité managée vous sera utile pour autoriser le cluster à accéder au coffre de clés. Pour configurer des identités managées affectées au système, consultez [Identités managées](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Pour créer un coffre de clés via PowerShell, appelez la commande [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Le coffre de clés que vous utilisez pour stocker des clés gérées par le client pour le chiffrement Azure Data Explorer doit disposer de deux paramètres de protection de clés, **Suppression réversible** et **Ne pas vider**. Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs dans l’exemple ci-dessous.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès au coffre de clés

Configurez ensuite la stratégie d’accès au coffre de clés de sorte que le cluster dispose des autorisations nécessaires pour y accéder. À cette étape, vous utiliserez l’identité managée affectée au système que vous avez précédemment attribuée au cluster. Pour définir la stratégie d’accès du coffre de clés, appelez la commande [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs et utilisez les variables définies dans les exemples précédents.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Créer une clé

Créez ensuite une clé dans le coffre de clés. Pour créer une clé, appelez la commande [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs et utilisez les variables définies dans les exemples précédents.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
