---
title: PowerShell - Supprimer un protecteur TDE - Azure SQL Database | Microsoft Docs
description: Guide pratique pour répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec la prise en charge Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 73fcb2753fa7eb15f34b04ddc5bb0b55c4636623
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847803"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Supprimer un protecteur Transparent Data Encryption (TDE) à l’aide de PowerShell

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement
- Vous devez disposer d’Azure PowerShell est installé et en cours d’exécution. 
- Ce guide pratique part du principe que vous utilisez déjà une clé Azure Key Vault comme protecteur TDE pour une entité SQL Azure Database ou Data Warehouse. Consultez [Transparent Data Encryption with Bring Your Own Key support for Azure SQL Database and Data Warehouse](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption avec la prise en charge Bring Your Own Key pour Azure SQL Database et Data Warehouse) pour en savoir plus.

## <a name="overview"></a>Présentation

Ce guide pratique décrit comment répondre à un protecteur TDE potentiellement compromis pour une entité Azure SQL Database ou Data Warehouse à l’aide de TDE avec des clés gérées par le client dans Azure Key Vault - Prise en charge du modèle BYOK (Bring Your Own Key). Pour en savoir plus sur la prise en charge BYOK pour TDE, consultez la [page de vue d’ensemble](transparent-data-encryption-byok-azure-sql.md). 

Les procédures suivantes doivent uniquement être effectuées dans les cas extrêmes ou dans des environnements de test. Lisez attentivement ce guide pratique, car la suppression de protecteurs TDE activement utilisés d’Azure Key Vault peut entraîner **une perte de données**. 

Si vous suspectez qu’une clé est compromise, en cas d’accès non autorisé d’un utilisateur ou d’un service à cet clé, par exemple, nous vous recommandons de supprimer cette clé.

Gardez à l’esprit qu’une fois le protecteur TDE supprimé dans Key Vault, **toutes les connexions aux bases de données chiffrées du serveur sont bloquées et mises hors connexion, ces bases de données sont ensuite supprimées dans les 24 heures**. Les anciennes sauvegardes chiffrées avec la clé compromise ne sont plus accessibles.

Ce guide pratique expose deux approches possibles en fonction du résultat souhaité après la réponse aux incidents :

- Pour garder les bases de données Azure SQL/Data Warehouse **accessibles**
- Pour rendre les bases de données Azure SQL/Data Warehouse **inaccessibles**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Pour garder les ressources chiffrées accessibles

1. Créez une [clé dans Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Assurez-vous que cette clé est créée dans un coffre de clés distinct à partir du protecteur TDE potentiellement compromis, étant donné que le contrôle d’accès est configuré au niveau du coffre.
2. Ajouter la nouvelle clé pour le serveur à l’aide de la [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) et [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) applets de commande et mettez-le à jour en tant que nouveau protecteur de TDE du serveur.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Assurez-vous que le serveur et tous les réplicas ont mis à jour pour le nouveau protecteur TDE à l’aide de la [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) applet de commande. 

   >[!NOTE]
   > Quelques minutes peuvent être nécessaires pour que le nouveau protecteur TDE soit distribué à l’ensemble des bases de données et bases de données secondaires du serveur.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faites une [sauvegarde de la nouvelle clé](/powershell/module/az.keyvault/backup-azkeyvaultkey) dans Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Supprimer la clé compromise dans Key Vault à l’aide de la [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) applet de commande. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Pour restaurer une clé dans Key Vault à l’avenir à l’aide de la [AzKeyVaultKey de restauration](/powershell/module/az.keyvault/restore-azkeyvaultkey) applet de commande :
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Pour rendre les ressources chiffrées inaccessibles

1. Supprimez les bases de données chiffrées avec la clé potentiellement compromise.

   Les bases de données et les fichiers journaux étant automatiquement sauvegardés, une restauration dans le temps peut donc être effectuée à tout moment (à condition que vous fournissiez la clé). Les bases de données doivent être supprimées avant de supprimer un protecteur TDE actif pour empêcher une perte de données éventuelle de 10 minutes des transactions les plus récentes. 
2. Sauvegardez l’élément de clé du protecteur TDE dans Key Vault.
3. Supprimez la clé potentiellement compromise de Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pivoter le protecteur TDE d’un serveur pour se conformer à des exigences de sécurité : [Faites pivoter le protecteur Transparent Data Encryption à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Prise en main de la prise en charge de Bring Your Own Key pour TDE : [ Activer TDE à l’aide de votre propre clé depuis Key Vault à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
