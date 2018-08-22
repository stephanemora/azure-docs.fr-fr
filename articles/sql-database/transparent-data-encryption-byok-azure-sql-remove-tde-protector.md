---
title: PowerShell - Supprimer un protecteur TDE - Azure SQL Database | Microsoft Docs
description: Guide pratique pour répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec la prise en charge Bring Your Own Key (BYOK).
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043362"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Supprimer un protecteur Transparent Data Encryption (TDE) à l’aide de PowerShell
## <a name="prerequisites"></a>Prérequis
- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement
- Azure PowerShell version 4.2.0 ou ultérieure doit être installé et en cours d’exécution. 
- Ce guide pratique part du principe que vous utilisez déjà une clé Azure Key Vault comme protecteur TDE pour une entité SQL Azure Database ou Data Warehouse. Consultez [Transparent Data Encryption with Bring Your Own Key support for Azure SQL Database and Data Warehouse](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption avec la prise en charge Bring Your Own Key pour Azure SQL Database et Data Warehouse) pour en savoir plus.

## <a name="overview"></a>Vue d’ensemble
Ce guide pratique décrit comment répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec la prise en charge Bring Your Own Key (BYOK). Pour en savoir plus sur la prise en charge BYOK pour TDE, consultez la [page de vue d’ensemble](transparent-data-encryption-byok-azure-sql.md). 

Les procédures suivantes doivent uniquement être effectuées dans les cas extrêmes ou dans des environnements de test. Lisez attentivement ce guide pratique, car la suppression de protecteurs TDE activement utilisés d’Azure Key Vault peut entraîner **une perte de données**. 

Si vous suspectez qu’une clé est compromise, en cas d’accès non autorisé d’un utilisateur ou d’un service à cet clé, par exemple, nous vous recommandons de supprimer cette clé.

Gardez à l’esprit qu’une fois le protecteur TDE supprimé dans Key Vault, **toutes les connexions aux bases de données chiffrées du serveur sont bloquées et mises hors connexion, ces bases de données sont ensuite supprimées dans les 24 heures**. Les anciennes sauvegardes chiffrées avec la clé compromise ne sont plus accessibles.

Ce guide pratique expose deux approches possibles en fonction du résultat souhaité après la réponse aux incidents :
- Pour garder les entités Azure SQL Database / Data Warehouse **accessibles**
- Pour rendre les entités Azure SQL Database / Data Warehouse **inaccessibles**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Pour garder les ressources chiffrées accessibles
1. Créez une [clé dans Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Assurez-vous que cette clé est créée dans un coffre de clés distinct à partir du protecteur TDE potentiellement compromis, étant donné que le contrôle d’accès est configuré au niveau du coffre. 
2. Ajoutez la nouvelle clé au serveur en utilisant les cmdlets [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) et [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) et mettez-le à jour en tant que nouveau protecteur TDE du serveur.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Assurez-vous que le serveur et tous les réplicas ont été mis à jour pour le nouveau protecteur TDE à l’aide de la cmdlet [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector). 

   >[!NOTE]
   > Quelques minutes peuvent être nécessaires pour que le nouveau protecteur TDE soit distribué à l’ensemble des bases de données et bases de données secondaires du serveur.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faites une [sauvegarde de la nouvelle clé](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) dans Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Supprimez la clé compromise de Key Vault à l’aide de la cmdlet [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey). 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Pour restaurer ultérieurement une clé dans Key Vault à l’aide de la cmdlet [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) :
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Pour rendre les ressources chiffrées inaccessibles
1. Supprimez les bases de données chiffrées avec la clé potentiellement compromise.
Les bases de données et les fichiers journaux étant automatiquement sauvegardés, une restauration dans le temps peut donc être effectuée à tout moment (à condition que vous fournissiez la clé). Les bases de données doivent être supprimées avant de supprimer un protecteur TDE actif pour empêcher une perte de données éventuelle de 10 minutes des transactions les plus récentes. 
2. Sauvegardez l’élément de clé du protecteur TDE dans Key Vault.
3. Supprimez la clé potentiellement compromise de Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pivoter le protecteur TDE d’un serveur pour respecter les exigences de sécurité : [Rotate the Transparent Data Encryption (TDE) protector using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) (Faire pivoter le protecteur Transparent Data Encryption (TDE) à l’aide de PowerShell)

- Découvrez la prise en charge Bring Your Own Key pour TDE : [Turn on TDE using your own key from Key Vault using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Activer TDE à l’aide de votre propre clé depuis Key Vault à l’aide de PowerShell)
