---
ms.assetid: ''
title: Compte de stockage managé Azure Key Vault – CLI
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e110a24af6a0d49ea949db6bc30ab344c81d0f48
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619280"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Compte de stockage managé Azure Key Vault – CLI

> [!NOTE]
> [Le stockage Azure prend maintenant en charge l’autorisation AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Nous vous recommandons d’utiliser Azure Active Directory pour l’authentification et autorisation sur le stockage pour ne pas que les utilisateurs se préoccupent de la rotation des clés de leurs comptes de stockage.

- Azure Key Vault gère les clés d’un compte de stockage Azure (ASA).
    - En interne, Azure Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure.    
    - Azure Key Vault régénère (fait tourner) les clés régulièrement.
    - Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
    - Azure Key Vault gère les clés des comptes de stockage ainsi que des comptes de stockage Classic.

<a name="prerequisites"></a>Prérequis
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Installer Azure CLI   
2. [Créer un compte de stockage](https://azure.microsoft.com/services/storage/)
    - Suivez les étapes de ce [document](https://docs.microsoft.com/azure/storage/) pour créer un compte de stockage.  
    - **Instructions de nommage :** Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Instructions étape par étape sur la façon d'utiliser Key Vault pour gérer les clés du compte de stockage
--------------------------------------------------------------------------------
Dans les instructions ci-dessous, nous attribuons au service Key Vault des autorisations d’opérateur sur votre compte de stockage.

> [!NOTE]
> Veuillez noter qu'une fois que vous avez configuré les clés du compte de stockage managé Azure Key Vault, elles ne doivent **PLUS**être modifiées, sauf via Key Vault. Clés du compte de stockage managé signifie que Key Vault gère la rotation de la clé du compte de stockage

1. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage que vous voulez gérer.

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Obtenez l’ID d’application auprès du principal de service d’Azure Key Vault. 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Attribuez le rôle d’opérateur de clés de stockage à l’identité Azure Key Vault.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Créez un compte de stockage managé Key Vault.     <br /><br />
   Ci-dessous, nous définissons une période de regénération de 90 jours. Après 90 jours, Key Vault regénère « key1 » et remplace la clé active « key2 » par « key1 ».
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Si l’utilisateur n’a pas créé le compte de stockage et ne dispose pas des autorisations sur le compte de stockage, les étapes ci-dessous définissent les autorisations sur votre compte pour être sûr de pouvoir gérer toutes les autorisations de stockage dans le coffre de clés.
 > [!NOTE] 
    Si l’utilisateur ne dispose pas des autorisations sur le compte de stockage, nous commençons par obtenir l’ID d’objet de l’utilisateur.

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Applets de commande Azure CLI correspondantes
- [Applets de commande de stockage Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Cmdlets PowerShell appropriées

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Voir aussi

- [Présentation des clés, des secrets et des certificats](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog de l’équipe Key Vault](https://blogs.technet.microsoft.com/kv/)
