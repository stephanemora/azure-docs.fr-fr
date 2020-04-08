---
title: Supprimer le protecteur TDE – PowerShell
description: Guide pratique pour répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec la prise en charge Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067209"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Supprimer un protecteur Transparent Data Encryption (TDE) à l’aide de PowerShell

## <a name="prerequisites"></a>Prérequis

- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement
- Azure PowerShell doit être installé et en cours d’exécution.
- Ce guide pratique part du principe que vous utilisez déjà une clé Azure Key Vault comme protecteur TDE pour une base de données Azure SQL ou un entrepôt de données. Consultez [Transparent Data Encryption with Bring Your Own Key support for Azure SQL Database and Data Warehouse](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption avec la prise en charge Bring Your Own Key pour Azure SQL Database et Data Warehouse) pour en savoir plus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Pour des instructions d’installation du module Az, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps). Pour des applets de commande spécifiques, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager (RM) est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020.  Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour l’installation, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Vue d’ensemble

Ce guide pratique décrit comment répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec des clés gérées par le client dans Azure Key Vault - Prise en charge du modèle BYOK (Bring Your Own Key). Pour en savoir plus sur la prise en charge BYOK pour TDE, consultez la [page de vue d’ensemble](transparent-data-encryption-byok-azure-sql.md).

Les procédures suivantes doivent uniquement être effectuées dans les cas extrêmes ou dans des environnements de test. Lisez attentivement le guide pratique, car la suppression des protecteurs TDE activement utilisés d’Azure Key Vault entraîne une **non-disponibilité de la base de données**.

Si vous suspectez qu’une clé est compromise, en cas d’accès non autorisé d’un utilisateur ou d’un service à cet clé, par exemple, nous vous recommandons de supprimer cette clé.

Gardez à l’esprit qu’une fois le protecteur TDE supprimé au sein de Key Vault, après un délai maximal de 10 minutes, toutes les bases de données chiffrées se mettent à refuser l’ensemble des connexions avec le message d’erreur correspondant, puis passent à l’état [Inaccessible](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

Les étapes suivantes indiquent comment vérifier que les empreintes du protecteur TDE sont toujours en cours d’utilisation par des fichiers journaux virtuels d’une base de données spécifique.
Pour trouver l’empreinte du protecteur TDE actuel de la base de données et l’ID de base de données, exécutez la commande suivante :

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

La requête suivante retourne les fichiers journaux virtuels et les empreintes respectives du chiffreur en cours d’utilisation. Chaque empreinte différente fait référence à une autre clé dans Azure Key Vault (AKV) :

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

La commande PowerShell **Get-AzureRmSqlServerKeyVaultKey** fournit l’empreinte du protecteur TDE utilisé dans la requête, afin que vous puissiez voir quelles clés conserver et lesquelles supprimer dans AKV. Seules les clés qui ne sont plus utilisées par la base de données peuvent être supprimées de façon sécurisée d’Azure Key Vault.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

La commande PowerShell **az sql server key show** fournit l’empreinte du protecteur TDE utilisé dans la requête. Vous pouvez ainsi voir quelles clés conserver et supprimer dans AKV. Seules les clés qui ne sont plus utilisées par la base de données peuvent être supprimées de façon sécurisée d’Azure Key Vault.

* * *

Ce guide pratique expose deux approches possibles en fonction du résultat souhaité après la réponse aux incidents :

- Pour garder les bases de données Azure SQL/entrepôts de données **accessibles**
- Pour rendre les bases de données Azure SQL/entrepôts de données **inaccessibles**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Pour garder les ressources chiffrées accessibles

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Créez une [clé dans Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assurez-vous que cette clé est créée dans un coffre de clés distinct à partir du protecteur TDE potentiellement compromis, étant donné que le contrôle d’accès est configuré au niveau du coffre.

2. Ajoutez la nouvelle clé au serveur en utilisant les cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) et [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) et mettez-la à jour en tant que nouveau protecteur TDE du serveur.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Assurez-vous que le serveur et tous les réplicas ont été mis à jour pour le nouveau protecteur TDE à l’aide de la cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector).

   > [!NOTE]
   > Quelques minutes peuvent être nécessaires pour que le nouveau protecteur TDE soit distribué à l’ensemble des bases de données et bases de données secondaires du serveur.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faites une [sauvegarde de la nouvelle clé](/powershell/module/az.keyvault/backup-azkeyvaultkey) dans Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Supprimez la clé compromise de Key Vault à l’aide de la cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey).

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Pour restaurer ultérieurement une clé dans Key Vault à l’aide de la cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour obtenir des informations de référence sur les commandes, consultez le [coffre de clés Azure CLI](/cli/azure/keyvault/key).

1. Créez une [clé dans Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Assurez-vous que cette clé est créée dans un coffre de clés distinct à partir du protecteur TDE potentiellement compromis, étant donné que le contrôle d’accès est configuré au niveau du coffre.

2. Ajoutez la nouvelle clé au serveur et mettez-la à jour en tant que nouveau protecteur TDE du serveur.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Assurez-vous que le serveur et tous les réplicas ont été mis à jour vers le nouveau protecteur TDE.

   > [!NOTE]
   > Quelques minutes peuvent être nécessaires pour que le nouveau protecteur TDE soit distribué à l’ensemble des bases de données et bases de données secondaires du serveur.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Faites une sauvegarde de la nouvelle clé dans Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Supprimez la clé compromise de Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Pour restaurer une clé dans Key Vault à l’avenir.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Pour rendre les ressources chiffrées inaccessibles

1. Supprimez les bases de données chiffrées avec la clé potentiellement compromise.

   Les bases de données et les fichiers journaux étant automatiquement sauvegardés, une restauration dans le temps peut donc être effectuée à tout moment (à condition que vous fournissiez la clé). Les bases de données doivent être supprimées avant de supprimer un protecteur TDE actif pour empêcher une perte de données éventuelle de 10 minutes des transactions les plus récentes.

2. Sauvegardez l’élément de clé du protecteur TDE dans Key Vault.
3. Supprimez la clé potentiellement compromise de Key Vault.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pivoter le protecteur TDE d’un serveur pour se conformer à des exigences de sécurité : [Faites pivoter le protecteur Transparent Data Encryption à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Prise en main de la prise en charge de Bring Your Own Key pour TDE : [ Activer TDE à l’aide de votre propre clé depuis Key Vault à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
