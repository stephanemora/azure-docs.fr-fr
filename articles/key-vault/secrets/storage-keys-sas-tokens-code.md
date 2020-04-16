---
title: Compte de stockage managé Azure Key Vault - version PowerShell
description: La fonctionnalité de compte de stockage managé assure une intégration fluide entre Azure Key Vault et un compte de stockage Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427457"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Récupérer les jetons de signature d’accès partagé dans le code

Vous pouvez gérer votre compte de stockage avec les [jetons de signature d’accès partagé](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) dans votre coffre de clés. Cet article fournit des exemples de code C# qui extraient un jeton SAS et effectuent des opérations avec celui-ci.  Pour plus d’informations sur la façon de créer et de stocker des jetons SAS, consultez [Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure](overview-storage-keys.md) ou [Gérer les clés de compte de stockage avec Key Vault et Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemples de code

Dans cet exemple, le code extrait un jeton SAS de votre coffre de clés, l’utilise pour créer un compte de stockage et crée un client de service BLOB.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Si votre jeton de signature d’accès partagé est sur le point d’expirer, vous pouvez le récupérer de votre coffre de clés, puis mettre à jour le code.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure](overview-storage-keys.md) ou [Azure PowerShell](overview-storage-keys-powershell.md).
- Consultez [Exemples de clés de compte de stockage managé](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).
- [Informations de référence PowerShell sur Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
