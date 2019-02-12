---
ms.assetid: ''
title: Compte de stockage managé Azure Key Vault – CLI
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 152e1e5892e3a72286205c2f5bf4e18b2a2bcbf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814841"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Compte de stockage managé Azure Key Vault – CLI

> [!NOTE]
> [L’intégration du stockage Azure avec Azure Active Directory (Azure AD) est désormais disponible en préversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Nous recommandons d’utiliser Azure AD pour l’authentification et l’autorisation, offrant un accès au stockage Azure via un jeton OAuth2, à l’instar d’Azure Key Vault. Vous pouvez ainsi :
> - Authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage. 
> - Utiliser une [identité Azure AD managée](/azure/active-directory/managed-identities-azure-resources/) lors de l’exécution sur Azure. Les identités managées suppriment totalement l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application.
> - Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations, ce qui est également pris en charge par Key Vault.

- Azure Key Vault gère les clés d’un compte de stockage Azure (ASA).
    - En interne, Azure Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure.    
    - Azure Key Vault régénère (fait tourner) les clés régulièrement.
    - Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
    - Azure Key Vault gère les clés des comptes de stockage ainsi que des comptes de stockage Classic.
    
> [!IMPORTANT]
> Un locataire Azure AD fournit à chaque application inscrite un **[principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object)**, qui représente l’identité de l’application. L’ID d’application du principal de service est utilisé quand il reçoit l’autorisation d’accéder à d’autres ressources Azure, via le contrôle d’accès en fonction du rôle (RBAC). Key Vault étant une application Microsoft, elle est préinscrite dans tous les locataires Azure AD sous le même ID d’application dans chaque cloud Azure :
> - Les locataires Azure AD dans le cloud Azure Government utilisent l’ID d’application `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Les locataires Azure AD dans le cloud public Azure et tous les autres utilisent l’ID d’application `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Prérequis
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Installer Azure CLI   
2. [Créer un compte de stockage](https://azure.microsoft.com/services/storage/)
    - Suivez les étapes de ce [document](https://docs.microsoft.com/azure/storage/) pour créer un compte de stockage.  
    - **Aide pour le nommage :** Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Instructions étape par étape sur la façon d'utiliser Key Vault pour gérer les clés du compte de stockage
--------------------------------------------------------------------------------
Dans les instructions ci-dessous, nous attribuons au service Key Vault des autorisations d’opérateur sur votre compte de stockage.

> [!NOTE]
> Veuillez noter qu'une fois que vous avez configuré les clés du compte de stockage managé Azure Key Vault, elles ne doivent **PLUS**être modifiées, sauf via Key Vault. Clés du compte de stockage managé signifie que Key Vault gère la rotation de la clé du compte de stockage

1. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage que vous voulez gérer.

    ```
    az storage account show -n storageaccountname 
    ```
    Copiez le champ ID à partir du résultat de la commande ci-dessus
    
2. Obtenez l'ID d'objet du principal de service d'Azure Key Vault en exécutant la commande ci-dessous

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    Si l'exécution de cette commande aboutit, recherchez l'ID d'objet dans le résultat
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Attribuez le rôle d’opérateur de clés de stockage à l’identité Azure Key Vault.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Créez un compte de stockage managé Key Vault.     <br /><br />
   Ci-dessous, nous définissons une période de regénération de 90 jours. Après 90 jours, Key Vault regénère « key1 » et remplace la clé active « key2 » par « key1 ». Il désigne maintenant Key1 comme la clé active. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    Si l’utilisateur n’a pas créé le compte de stockage et ne dispose pas des autorisations sur le compte de stockage, les étapes ci-dessous définissent les autorisations sur votre compte pour être sûr de pouvoir gérer toutes les autorisations de stockage dans le coffre de clés.
    
 > [!NOTE] 
 > Si l’utilisateur ne dispose pas des autorisations sur le compte de stockage, nous commençons par obtenir l’ID d’objet de l’utilisateur.


    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>Comment accéder à votre compte de stockage avec des jetons SAS

Dans cette section, nous allons étudier comment vous pouvez effectuer des opérations sur votre compte de stockage en extrayant des [jetons SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) à partir de Key Vault

Dans la section ci-dessous, nous montrons comment extraire la clé de votre compte de stockage qui est stockée dans Key Vault et l’utiliser pour créer une définition SAS (signature d’accès partagé) pour votre compte de stockage.

> [!NOTE] 
  Il existe 3 façons de s’authentifier sur Key Vault comme vous pouvez le lire dans les [concepts de base](key-vault-whatis.md#basic-concepts)
> - Utilisation de MSI (Managed Service Identity), fortement recommandée
> - Utilisation d’un principal de service et d’un certificat 
> - Utilisation d’un principal de service et d’un mot de passe, non recommandée

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Si votre jeton SAS est sur le point d’expirer, extrayez-le à nouveau à partir de Key Vault, puis mettez à jour le code

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>Applets de commande Azure CLI correspondantes
[Applets de commande de stockage Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

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
