---
title: 'PowerShell et CLI : Activer TDE SQL - votre clé - Azure SQL Database | Microsoft Docs'
description: Découvrez comment configurer des entités Azure SQL Database et Data Warehouse pour commencer à utiliser Transparent Data Encryption (TDE) pour le chiffrement au repos à l’aide de PowerShell ou CLI.
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
ms.date: 12/04/2018
ms.openlocfilehash: be73f5cb7db232538f301b2eb56bf61267fce5d5
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566718"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell et CLI : Activer Transparent Data Encryption à l’aide de votre propre clé Azure Key Vault

Cet article explique comment utiliser une clé Azure Key Vault pour Transparent Data Encryption (TDE) sur une entité SQL Database ou Data Warehouse. Pour en savoir plus sur la prise en charge TDE avec Bring Your Own Key (BYOK), consultez [Transparent Data Encryption with Bring Your Own Key support for Azure SQL Database and Data Warehouse](transparent-data-encryption-byok-azure-sql.md) (Transparent Data Encryption avec la prise en charge Bring Your Own Key pour Azure SQL Database et Data Warehouse). 

## <a name="prerequisites-for-powershell"></a>Prérequis pour PowerShell

- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
- [Recommandé mais facultatif] Avoir un module de sécurité matériel (HSM) ou un magasin de clés local pour créer une copie locale de l’élément de clé du protecteur TDE.
- Azure PowerShell version 4.2.0 ou ultérieure doit être installé et en cours d’exécution. 
- Créez un coffre Azure Key Vault et une clé pour utiliser TDE.
   - [Instructions pour utiliser PowerShell à partir de Key Vault](../key-vault/key-vault-get-started.md)
   - [Instructions pour utiliser un module de sécurité matériel (HSM) et Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Le coffre de clés doit avoir la propriété suivante à utiliser pour TDE :
   - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
   - [Utilisation de la suppression réversible Key Vault avec l’interface CLI](../key-vault/key-vault-soft-delete-powershell.md) 
- La clé doit avoir les attributs suivants à utiliser pour TDE :
   - Aucune date d’expiration
   - Non activée
   - En mesure d’effectuer des opérations *get*, *wrap key*, *unwrap key*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Étape 1. Assigner une identité Azure AD à votre serveur 

Si vous avez un serveur existant, utilisez ce qui suit pour ajouter une identité Azure AD à votre serveur :

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Si vous créez un serveur, utilisez la cmdlet [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) avec la balise-Identity pour ajouter une identité Azure AD lors de la création du serveur :

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Étape 2. Accorder des autorisations Key Vault à votre serveur

Utilisez la cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) pour accorder à votre serveur l’accès au coffre de clés avant d’utiliser une clé à partir de celui-ci pour TDE.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Étape 3. Ajouter la clé Key Vault au serveur et définir le protecteur TDE

- Utilisez la cmdlet [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) pour ajouter la clé de Key Vault au serveur.
- Utilisez la cmdlet [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) pour définir la clé en tant que protecteur TDE pour toutes les ressources de serveur.
- Utilisez la cmdlet [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) pour confirmer que le protecteur TDE a été configuré comme prévu.

> [!Note]
> La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.
> 

>[!Tip]
>Voici un exemple de KeyId de Key Vault : https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Étape 4. Activer TDE 

Utilisez la cmdlet [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) pour activer TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

TDE est maintenant activé pour l’entrepôt de données ou la base de données, avec une clé de chiffrement dans Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Étape 5. Vérifier l’état de chiffrement et l’activité de chiffrement

Utilisez la cmdlet [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) pour obtenir l’état de chiffrement et la cmdlet [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) pour vérifier la progression du chiffrement d’une base de données ou d’un entrepôt de données.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Autres cmdlets PowerShell utiles

- Utilisez la cmdlet [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) pour désactiver TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Utilisez la cmdlet [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) pour renvoyer la liste de clés Key Vault ajoutées au serveur.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Utilisez la cmdlet [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) pour supprimer une clé Key Vault du serveur.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Résolution de problèmes

Vérifiez les points suivants en cas de problème :
- Si le coffre de clés est introuvable, vérifiez que vous êtes dans l’abonnement approprié à l’aide de la cmdlet [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Si la nouvelle clé ne peut pas être ajoutée au serveur ou si elle ne peut pas être mise à jour en tant que protecteur TDE, vérifiez les points suivants :
   - La clé ne doit pas avoir de date d’expiration
   - La clé doit avoir les opérations *get*, *wrap key* et *unwrap key* activées.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pivoter le protecteur TDE d’un serveur pour se conformer à des exigences de sécurité : [Faites pivoter le protecteur Transparent Data Encryption à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- En cas de risque pour la sécurité, découvrez comment supprimer un protecteur TDE potentiellement compromis : [Supprimez une clé potentiellement compromise](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Prérequis pour CLI

- Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
- [Recommandé mais facultatif] Avoir un module de sécurité matériel (HSM) ou un magasin de clés local pour créer une copie locale de l’élément de clé du protecteur TDE.
- Interface de ligne de commande Azure, version 2.0 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration de l’interface de ligne de commande multiplateforme Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Créez un coffre Azure Key Vault et une clé pour utiliser TDE.
   - [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0](../key-vault/key-vault-manage-with-cli2.md)
   - [Instructions pour utiliser un module de sécurité matériel (HSM) et Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Le coffre de clés doit avoir la propriété suivante à utiliser pour TDE :
   - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
   - [Utilisation de la suppression réversible Key Vault avec l’interface CLI](../key-vault/key-vault-soft-delete-cli.md) 
- La clé doit avoir les attributs suivants à utiliser pour TDE :
   - Aucune date d’expiration
   - Non activée
   - En mesure d’effectuer des opérations *get*, *wrap key*, *unwrap key*
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Étape 1. Créer un serveur et assigner une identité Azure AD à votre serveur
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -i 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Étape 2. Accorder des autorisations Key Vault à votre serveur
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Étape 3. Ajouter la clé Key Vault au serveur et définir le protecteur TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> La longueur combinée du nom du coffre de clés et du nom de la clé ne peut pas dépasser 94 caractères.
> 

>[!Tip]
>Voici un exemple de KeyId de Key Vault : https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Étape 4. Activer TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

TDE est maintenant activé pour l’entrepôt de données ou la base de données, avec une clé de chiffrement dans Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Étape 5. Vérifier l’état de chiffrement et l’activité de chiffrement

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Informations de référence sur CLI SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

