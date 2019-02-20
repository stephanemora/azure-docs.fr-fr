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
ms.date: 12/06/2018
ms.openlocfilehash: 45cd4e884530836d515e0c6cce8a6fc9be109d88
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992005"
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

- Ce guide pratique part du principe que vous utilisez déjà une clé Azure Key Vault comme protecteur TDE pour une entité SQL Azure Database ou Data Warehouse. Voir [Transparent Data Encryption avec l’intégration d’Azure Key Vault - Prise en charge BYOK](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell version 3.7.0.x ou ultérieure doit être installé et en cours d’exécution. 
- [Recommandé mais facultatif] Créez tout d’abord le matériel de clé pour le protecteur TDE dans un module de sécurité matériel (HSM) ou le magasin de clés local, puis importez le matériel de clé dans Azure Key Vault. Suivez le [instructions sur l’utilisation d’un module de sécurité matériel (HSM) et Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) pour en savoir plus.

## <a name="manual-key-rotation"></a>Rotation des clés manuelle

La rotation des clés manuelle utilise les applets de commande [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) et [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) pour ajouter une toute nouvelle clé, qui peut être créée sous un nouveau nom de clé, voire un autre coffre de clés. L’utilisation de cette approche prend en charge l’ajout de la même clé à différents coffres de clés afin de prendre en charge les scénarios de haute disponibilité et de géo-reprise.

>[!NOTE]
>La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Autres applets de commande PowerShell utiles

- Pour faire passer le protecteur TDE du mode géré par Microsoft au mode BYOK, utilisez l’applet de commande [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Pour faire passer le protecteur TDE du mode BYOK au mode géré par Microsoft, utilisez l’applet de commande [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Étapes suivantes

- En cas de risque de sécurité, découvrez comment supprimer un protecteur TDE potentiellement compromis : [Supprimer une clé potentiellement compromise](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Prise en main de l’intégration d’Azure Key Vault et de la prise en charge Bring Your Own Key pour TDE : [ Activer TDE à l’aide de votre propre clé depuis Key Vault à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
