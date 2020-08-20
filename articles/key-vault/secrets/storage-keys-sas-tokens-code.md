---
title: Récupérer les jetons de signature d’accès partagé dans le code | Azure Key Vault
description: La fonctionnalité de compte de stockage managé assure une intégration fluide entre Azure Key Vault et un compte de stockage Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584911"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Récupérer les jetons de signature d’accès partagé dans le code

Vous pouvez gérer votre compte de stockage avec les jetons de signature d’accès partagé(SAS) dans votre coffre de clés. Pour plus d’informations, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des SAS](../../storage/common/storage-sas-overview.md).

Cet article fournit des exemples de code .NET qui extraient un jeton SAS et effectuent des opérations avec celui-ci. Pour plus d’informations sur la façon de créer et de stocker des jetons SAS, consultez [Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure](overview-storage-keys.md) ou [Gérer les clés de compte de stockage avec Key Vault et Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemples de code

Dans cet exemple, le code extrait un jeton SAS de votre coffre de clés, l’utilise pour créer un compte de stockage et crée un client de service BLOB.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Si votre jeton de signature d’accès partagé est sur le point d’expirer, vous pouvez le récupérer de votre coffre de clés, puis mettre à jour le code.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [Accorder un accès limité aux ressources du Stockage Azure à l’aide des SAS](../../storage/common/storage-sas-overview.md).
- Découvrez comment [Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure](overview-storage-keys.md) ou [Azure PowerShell](overview-storage-keys-powershell.md).
- Consultez [Exemples de clés de compte de stockage managé](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).
