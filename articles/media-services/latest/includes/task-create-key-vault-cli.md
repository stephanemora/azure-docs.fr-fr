---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 431c63d9c5ac56e75b7a5e4f1655e5371367709e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103147"
---
<!-- Create a resource group -->

Utilisez la commande suivante pour créer un coffre Key Vault et une clé. Remplacez `your-resource-group-name`, `your-keyvault-name` et `your-key-name` par les valeurs que vous voulez utiliser. La commande suppose que vous avez déjà créé un groupe de ressources.

> [!NOTE]
> `--bypass AzureServices` permet à Media Services (et d’autres services Azure) d’accéder au coffre Key Vault alors que cet accès serait normalement bloqué par les ACL réseau Key Vault. Si `--enable-purge-protection` n’est pas défini, vous ne pourrez pas utiliser votre clé.

### <a name="create-the-key-vault"></a>Créer le coffre Key Vault

```azurecli-interactive

az keyvault create --resource-group <your-resource-group-name> --bypass AzureServices --enable-purge-protection --name <your-keyvault-name>

```

Exemple de réponse JSON :

```json
{
  "id": "/subscriptions/the-subscription-id/resourceGroups/your-resource-group-name/providers/Microsoft.KeyVault/vaults/your-keyvault-name",
  "location": "your-region",
  "name": "your-keyvault-name",
  "properties": {
    "accessPolicies": [
      {
        "applicationId": null,
        "objectId": "00000000-0000-0000-0000-000000000000",
        "permissions": {
          "certificates": [
            "get",
            "list",
            "delete",
            "create",
            "import",
            "update",
            "managecontacts",
            "getissuers",
            "listissuers",
            "setissuers",
            "deleteissuers",
            "manageissuers",
            "recover"
          ],
          "keys": [
            "get",
            "create",
            "delete",
            "list",
            "update",
            "import",
            "backup",
            "restore",
            "recover"
          ],
          "secrets": [
            "get",
            "list",
            "set",
            "delete",
            "backup",
            "restore",
            "recover"
          ],
          "storage": [
            "get",
            "list",
            "delete",
            "set",
            "update",
            "regeneratekey",
            "setsas",
            "listsas",
            "getsas",
            "deletesas"
          ]
        },
        "tenantId": "the-tenant-id"
      }
    ],
    "createMode": null,
    "enablePurgeProtection": true,
    "enableRbacAuthorization": null,
    "enableSoftDelete": true,
    "enabledForDeployment": false,
    "enabledForDiskEncryption": null,
    "enabledForTemplateDeployment": null,
    "networkAcls": null,
    "privateEndpointConnections": null,
    "provisioningState": "Succeeded",
    "sku": {
      "name": "standard"
    },
    "softDeleteRetentionInDays": 90,
    "tenantId": "the-tenant-id",
    "vaultUri": "https://your-keyvault-name.vault.azure.net/"
  },
  "resourceGroup": "your-resource-group-name",
  "tags": {},
  "type": "Microsoft.KeyVault/vaults"
}
```

### <a name="create-the-key"></a>Créer la clé

```azurecli-interactive
az keyvault key create --kty RSA --name your-key-name --vault-name your-keyvault-name

```

Exemple de réponse JSON :

```json

{
  "attributes": {
    "created": "2021-05-12T22:41:29+00:00",
    "enabled": true,
    "expires": null,
    "notBefore": null,
    "recoveryLevel": "Recoverable",
    "updated": "2021-05-12T22:41:29+00:00"
  },
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://your-keyvault-name.vault.azure.net/keys/your-key-name/your-subsription-id",
    "kty": "RSA",
    "n": "THISISTHEKEY51V9thvU7KsBUo/q1mEOcuxqt0qUcnx0IRO9YCL32fPjD/nnS8hKS5qkgUKfe2NRAtzVQ+elQAha65l7OsHu+TXmH/n/RPCgstpqSdCfiUR1JTmFYFRWdxCPwoKJMYaqlCEhn2Dkon3StTN0Id0sjRSA/YOLjgWU7YnVbntg5/048HgcTKn3PCWCuJc+P8hI/8Os5EAIpun62PffYwPX0/NIA1PY8wIB+sYEY0zxVGwWrCu7VgCo9xeqbMQEq5OenYmYpc+cjLozU/ohGhfWTpQU8d7fFypTHQraENDOFKEY",
    "p": null,
    "q": null,
    "qi": null,
    "t": null,
    "x": null,
    "y": null
  },
  "managed": null,
  "tags": null
}
```