---
title: PowerShell - Faire pivoter le protecteur TDE - Azure SQL Database | Microsoft Docs
description: Découvrez comment faire pivoter le protecteur Chiffrement transparent des données (TDE) pour un serveur SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330854"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Faire pivoter le protecteur Chiffrement transparent des données (TDE) à l’aide de PowerShell

Cet article décrit la rotation des clés pour un serveur SQL Azure à l’aide d’un protecteur TDE d’Azure Key Vault. La rotation du protecteur TDE d’un serveur SQL Azure implique le basculement vers une nouvelle clé asymétrique qui protège les bases de données sur le serveur. La rotation des clés est une opération en ligne et ne doit prendre que quelques secondes, car elle ne déchiffre et rechiffre que la clé de chiffrement des données de la base de données, et non la base de données entière.

Ce guide décrit deux options pour faire pivoter le protecteur TDE sur le serveur.

> [!NOTE]
> Un SQL Data Warehouse suspendu doit être remis en fonctionnement avant une rotation des clés.
>

> [!IMPORTANT]
> **Ne supprimez pas** les versions précédentes de la clé après une substitution.  Lorsque les clés sont substituées, certaines données sont toujours chiffrées avec les clés précédentes, comme les anciennes sauvegardes de base de données. 
>

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

- Ce guide pratique part du principe que vous utilisez déjà une clé Azure Key Vault comme protecteur TDE pour une base de données Azure SQL ou un entrepôt de données. Consultez [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md) (Chiffrement transparent des données avec prise en charge BYOK).
- Azure PowerShell doit être installé et en cours d’exécution.
- [Recommandé mais facultatif] Créez tout d’abord le matériel de clé pour le protecteur TDE dans un module de sécurité matériel (HSM) ou le magasin de clés local, puis importez le matériel de clé dans Azure Key Vault. Suivez le [instructions sur l’utilisation d’un module de sécurité matériel (HSM) et Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) pour en savoir plus.

## <a name="manual-key-rotation"></a>Rotation des clés manuelle

La rotation des clés manuelle utilise les cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) et [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) pour ajouter une toute nouvelle clé, qui peut être créée sous un nouveau nom de clé, voire un autre coffre de clés. L’utilisation de cette approche prend en charge l’ajout de la même clé à différents coffres de clés afin de prendre en charge les scénarios de haute disponibilité et de géo-reprise.

>[!NOTE]
>La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

## <a name="option-2-manual-rotation"></a>Option 2 : rotation manuelle

L’option utilise les cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) et [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) pour ajouter une toute nouvelle clé, qui peut être créée sous un nouveau nom de clé, voire un autre coffre de clés. 

>[!NOTE]
>La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Autres cmdlets PowerShell utiles

- Pour faire passer le protecteur TDE du mode géré par Microsoft au mode BYOK, utilisez la cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Pour faire passer le protecteur TDE du mode BYOK au mode géré par Microsoft, utilisez la cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Étapes suivantes

- En cas de risque de sécurité, découvrez comment supprimer un protecteur TDE potentiellement compromis : [Supprimer une clé potentiellement compromise](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Prise en main de l’intégration d’Azure Key Vault et de la prise en charge Bring Your Own Key pour TDE : [ Activer TDE à l’aide de votre propre clé depuis Key Vault à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
