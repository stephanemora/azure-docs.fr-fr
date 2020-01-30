---
title: Activer SQL TDE avec Azure Key Vault
description: Découvrez comment configurer des entités Azure SQL Database et Data Warehouse pour commencer à utiliser Transparent Data Encryption (TDE) pour le chiffrement au repos à l’aide de PowerShell ou CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 87a9db7d320a7d5b35234899c59884bcf2bf4b60
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721674"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell et CLI : Activer Transparent Data Encryption à l’aide d'une clé gérée par le client à partir d'Azure Key Vault

Cet article explique comment utiliser une clé Azure Key Vault pour Transparent Data Encryption (TDE) sur une entité SQL Database ou Data Warehouse. Pour en savoir plus sur TDE avec intégration Azure Key Vault - Prise en charge de BYOK (Bring Your Own Key), consultez [TDE avec clés managées dans Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Prérequis pour PowerShell

- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
- [Recommandé mais facultatif] Avoir un module de sécurité matériel (HSM) ou un magasin de clés local pour créer une copie locale de l’élément de clé du protecteur TDE.
- Azure PowerShell doit être installé et en cours d’exécution.
- Créez un coffre Azure Key Vault et une clé pour utiliser TDE.
  - [Instructions pour utiliser un module de sécurité matériel (HSM) et Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Le coffre de clés doit avoir la propriété suivante à utiliser pour TDE :
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) et protection contre le vidage
- La clé doit avoir les attributs suivants à utiliser pour TDE :
   - Aucune date d’expiration
   - Non activée
   - En mesure d’effectuer des opérations *get*, *wrap key*, *unwrap key*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour des instructions d’installation du module Az, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps). Pour des applets de commande spécifiques, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Pour obtenir des informations spécifiques sur Key Vault, consultez [Instructions pour utiliser PowerShell à partir de Key Vault](../key-vault/quick-create-powershell.md) et [Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager (RM) est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020.  Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Assigner une identité Azure AD à votre serveur

Si vous avez un serveur existant, utilisez ce qui suit pour ajouter une identité Azure AD à votre serveur :

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Si vous créez un serveur, utilisez la cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) avec la balise-Identity pour ajouter une identité Azure AD lors de la création du serveur :

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Accorder des autorisations Key Vault à votre serveur

Utilisez la cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour accorder à votre serveur l’accès au coffre de clés avant d’utiliser une clé à partir de celui-ci pour TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Ajouter la clé Key Vault au serveur et définir le protecteur TDE

- Utilisez le cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) pour récupérer l’ID de la clé à partir du coffre de clé
- Utilisez la cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) pour ajouter la clé de Key Vault au serveur.
- Utilisez la cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) pour définir la clé en tant que protecteur TDE pour toutes les ressources de serveur.
- Utilisez la cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) pour confirmer que le protecteur TDE a été configuré comme prévu.

> [!NOTE]
> La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.

> [!TIP]
> Voici un exemple de KeyId de Key Vault : https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Activer TDE

Utilisez la cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) pour activer TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

TDE est maintenant activé pour l’entrepôt de données ou la base de données, avec une clé de chiffrement dans Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Vérifier l’état de chiffrement et l’activité de chiffrement

Utilisez la cmdlet [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) pour obtenir l’état de chiffrement et la cmdlet [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) pour vérifier la progression du chiffrement d’une base de données ou d’un entrepôt de données.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour installer l’interface de ligne de commande dans la version 2.0 ou ultérieure requise et la connecter à votre abonnement Azure, consultez [Installer et configurer l’interface de ligne de commande multiplateforme Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Pour obtenir des informations spécifiques sur Key Vault, consultez [Gérer Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0](../key-vault/key-vault-manage-with-cli2.md) et [Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface CLI](../key-vault/key-vault-soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Assigner une identité Azure AD à votre serveur

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Conserver l'élément « principalID » lié à la création du serveur ; il correspond à l'ID d'objet utilisé pour attribuer les autorisations d'accès au coffre de clés de l'étape suivante

## <a name="grant-key-vault-permissions-to-your-server"></a>Accorder des autorisations Key Vault à votre serveur

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Conserver l’URI de clé ou keyID de la nouvelle clé pour l'étape suivante, par exemple : https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Ajouter la clé Key Vault au serveur et définir le protecteur TDE

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.

## <a name="turn-on-tde"></a>Activer TDE

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

TDE est maintenant activé pour l’entrepôt de données ou la base de données, avec une clé de chiffrement gérée par le client dans Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Vérifier l’état de chiffrement et l’activité de chiffrement

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Utilisez la cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) pour désactiver TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Utilisez la cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) pour renvoyer la liste de clés Key Vault ajoutées au serveur.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Utilisez la cmdlet [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) pour supprimer une clé Key Vault du serveur.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- Pour plus d’informations sur les paramètres de base de données généraux, consultez [az sql](/cli/azure/sql).

- Pour plus d’informations sur les paramètres de clé de coffre, consultez [AZ SQL Server Key](/cli/azure/sql/server/key).

- Pour plus d’informations sur les paramètres TDE, consultez [az sql server tde-key](/cli/azure/sql/server/tde-key) et [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Dépannage

Vérifiez les points suivants en cas de problème :

- Si le coffre de clés est introuvable, vérifiez que vous êtes dans le bon abonnement.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Si la nouvelle clé ne peut pas être ajoutée au serveur ou si elle ne peut pas être mise à jour en tant que protecteur TDE, vérifiez les points suivants :
   - La clé ne doit pas avoir de date d’expiration
   - La clé doit avoir les opérations *get*, *wrap key* et *unwrap key* activées.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pivoter le protecteur TDE d’un serveur pour se conformer à des exigences de sécurité : [Faites pivoter le protecteur Transparent Data Encryption à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- En cas de risque pour la sécurité, découvrez comment supprimer un protecteur TDE potentiellement compromis : [Supprimez une clé potentiellement compromise](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
