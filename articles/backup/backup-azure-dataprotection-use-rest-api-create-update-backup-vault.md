---
title: Créer une stratégie de Sauvegarde Azure pour blobs à l’aide d’une API REST.
description: Cet article explique comment créer une stratégie pour sauvegarder des blobs dans un compte de stockage à l’aide d’une API REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598637"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>Créer un coffre de sauvegarde Azure à l’aide d’une API REST

La nouvelle plateforme de protection des données de Sauvegarde Azure offre des fonctionnalités étendues pour la sauvegarde et la restauration de charges de travail plus récentes telles que les blobs dans des comptes de stockage, le disque managé et la plateforme PaaS du serveur postgreSQL. Elle vise à réduire la surcharge de gestion tout en facilitant l’organisation des sauvegardes. Un « coffre de sauvegarde » est la pierre angulaire de la plateforme de protection des données, et diffère du coffre Recovery Services.

Les étapes de création d’un coffre de Sauvegarde Azure à l’aide d’une API REST sont décrites dans la documentation de l’[API REST créer un coffre](/rest/api/dataprotection/backup-vaults/create-or-update). Utilisons ce document comme référence pour créer un coffre appelé « testBkpVault » dans la région « USA Ouest » sous le groupe de ressources « TestBkpVaultRG ».

Pour créer ou mettre à jour un coffre de sauvegarde Azure, utilisez l’opération *PUT* suivante.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>Créer une demande

Pour créer la demande *PUT*, vous devez impérativement utiliser le paramètre `{subscription-id}`. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](/cli/azure/manage-azure-subscriptions-azure-cli). Vous devez définir les paramètres `{resourceGroupName}` et `{vaultName}` pour vos ressources, ainsi que le paramètre `api-version`. Cet article utilise `api-version=2021-01-01`.

Les en-têtes suivants sont requis :

| En-tête de requête   | Description |
|------------------|-----------------|
| *Content-Type :*  | Obligatoire. Défini sur `application/json`. |
| *Authorization :* | Obligatoire. Défini sur un [jeton d’accès](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |

Pour plus d’informations sur la création de la demande, consultez [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Composants d’une demande/réponse de l’API REST).

## <a name="create-the-request-body"></a>Créer le corps de la demande

Les définitions courantes suivantes permettent de générer un corps de demande :

|Nom  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag facultatif       |
|location     |  true       |String         |   Emplacement de la ressource      |
|properties     |   true      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  Propriétés du coffre       |
|Identité     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    Identifie l’identificateur système unique de chaque ressource Azure.     |
|tags     |         | Object        |     Balises de ressource    |

Notez que le nom de l’espace de stockage et le nom du groupe de ressources sont fournis dans l’URI PUT. Le corps de la requête définit l’emplacement.

## <a name="example-request-body"></a>Exemple de corps de demande

Le corps de l’exemple suivant est utilisé pour créer un coffre dans la région « USA Ouest ». Spécifiez l’emplacement.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

Si vous souhaitez créer un coffre de sauvegarde et générer une identité attribuée par le système, le corps de demande suivant doit être spécifié.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>Réponses

La création d’un coffre de sauvegarde est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). ce qui signifie qu’elle crée une autre opération qui doit faire l’objet d’un suivi distinct.
Il existe deux réponses de réussite pour l’opération de création ou de mise à jour d’un coffre de sauvegarde :

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | OK        |
|201 Créé     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   Date de création      |
| Autres codes d’état  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

Pour plus d’informations sur les réponses des API REST, consultez [Process the response message](/rest/api/azure/#process-the-response-message) (Traiter le message de réponse).

### <a name="example-response"></a>Exemple de réponse

Une réponse *201 Créé* condensée à partir de l’exemple de corps de demande précédent affiche un *ID* assigné et l’état *provisioningState* *Succeeded* :

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>Étapes suivantes

[Créez une stratégie de sauvegarde pour sauvegarder les blobs dans ce coffre](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md).

Pour plus d’informations sur l’envoi de travaux à l’aide des API REST Azure, consultez les documents suivants :

- [API REST de fournisseur de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
