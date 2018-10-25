---
ms.assetid: ''
title: Clés de compte de stockage Azure Key Vault
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: adc8b84f0f22e85de88c4bd80c10a2a35d7b490a
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114598"
---
# <a name="azure-key-vault-storage-account-keys"></a>Clés de compte de stockage Azure Key Vault

> [!NOTE]
> [Le stockage Azure prend maintenant en charge l’autorisation AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Nous vous recommandons d’utiliser Azure Active Directory pour l’authentification et autorisation sur le stockage pour ne pas que les utilisateurs se préoccupent de la rotation des clés de leurs comptes de stockage.

- Azure Key Vault gère les clés d’un compte de stockage Azure (ASA).
    - En interne, Azure Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure.    
    - Azure Key Vault regénère (fait tourner) les clés régulièrement.
    - Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
    - Azure Key Vault gère les clés des comptes de stockage ainsi que des comptes de stockage Classic.

<a name="prerequisites"></a>Prérequis
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Installer Azure CLI   
2. [Créer un compte de stockage](https://azure.microsoft.com/services/storage/)
    - Suivez les étapes de ce [document](https://docs.microsoft.com/azure/storage/) pour créer un compte de stockage.  
    - **Instructions de nommage :** Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.        
      
<a name="step-by-step-instructions"></a>Instructions pas à pas
-------------------------

1. Obtenez l’ID de ressource du compte de Stockage Azure que vous souhaitez gérer.
    a. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage que vous voulez gérer.
    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
2. Obtenez l’ID d’application auprès du principal de service d’Azure Key Vault. 
    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
3. Attribuez le rôle d’opérateur de clés de stockage à l’identité Azure Key Vault.
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
4. Créez un compte de stockage managé Key Vault.     <br /><br />
   La commande ci-dessous demande à Key Vault de regénérer les clés d’accès de votre stockage régulièrement avec une période de regénération. Ci-dessous, nous définissons une période de regénération de 90 jours. Après 90 jours, Key Vault regénère « key1 » et remplace la clé active « key2 » par « key1 ».
   ### <a name="key-regeneration"></a>Regénération des clés
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-generate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Si l’utilisateur n’a pas créé le compte de stockage et ne dispose pas des autorisations sur le compte de stockage, les étapes ci-dessous définissent les autorisations sur votre compte pour être sûr de pouvoir gérer toutes les autorisations de stockage dans le coffre de clés.
    [!NOTE] Si l’utilisateur ne dispose pas des autorisations sur le compte de stockage, nous commençons par obtenir l’ID d’objet de l’utilisateur.

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover purge restore set setsas update
    ```

### <a name="relevant-powershell-cmdlets"></a>Applets de commande PowerShell appropriées

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
