---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 05/14/2021
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5b94f3644e55d10d589e726a9fc07affb963f36a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103151"
---
<!-- ### Set the the Key Vault policy -->

Pour utiliser cette commande, vous devez inclure Media Services `principalId` en tant que `object-id`. Si ce n’est déjà fait, utilisez `az ams account show --name <your-media-services-account-name> --resource-group <your-resource-group>` pour accéder à cet ID.

```azurecli-interactive
az keyvault set-policy --name <your-keyvault-name> --object-id <principalId> --key-permissions decrypt encrypt get list unwrapKey wrapKey
```

Exemple de réponse JSON :

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.KeyVault/vaults/your-keyvault-name",
  "location": "your-region",
  "name": "your-keyvault-name",
  "properties": {
    "accessPolicies": [
      {
        "applicationId": null,
        "objectId": "00000000-0000-0000-000000000000",
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
        "tenantId": "00000000-0000-0000-000000000000"
      },
      {
        "applicationId": null,
        "objectId": "00000000-0000-0000-000000000000",
        "permissions": {
          "certificates": null,
          "keys": [
            "encrypt",
            "get",
            "list",
            "wrapKey",
            "decrypt",
            "unwrapKey"
          ],
          "secrets": null,
          "storage": null
        },
        "tenantId": "00000000-0000-0000-000000000000"
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
    "tenantId": "00000000-0000-0000-000000000000",
    "vaultUri": "https://your-keyvault-name.vault.azure.net/"
  },
  "resourceGroup": "your-resource-group-name",
  "tags": {},
  "type": "Microsoft.KeyVault/vaults"
}
```
