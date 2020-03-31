---
title: Schéma des événements Azure Event Grid pour Azure Key Vault
description: Décrit les propriétés et le schéma qui sont fournis pour les événements Azure Key Vault avec Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082866"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schéma des événements Azure Event Grid pour Azure Key Vault (préversion)

Cet article fournit les propriétés et le schéma des événements dans [Azure Key Vault](../key-vault/index.yml), actuellement en version préliminaire. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Types d’événement disponibles

Un compte Azure Key Vault génère les types d’événements suivants :

| Nom complet de l’événement | Nom complet de l’événement | Description |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Nouvelle version du certificat créée | Déclenché lors de la création d’un nouveau certificat ou d’une nouvelle version de certificat. |
| Microsoft.KeyVault.CertificateNearExpiry | Expiration proche du certificat | Déclenché lorsque la version actuelle du certificat arrive à expiration. (La valeur par défaut est 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.CertificateExpired | Le certificat a expiré | Déclenché lorsque le certificat a expiré. |
| Microsoft.KeyVault.KeyNewVersionCreated | Nouvelle version de la clé créée | Déclenché lors de la création d’une nouvelle clé ou version de clé. |
| Microsoft.KeyVault.KeyNearExpiry | Expiration proche de la clé | Déclenché lorsque la version actuelle d’une clé va expirer. (La valeur par défaut est 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.KeyExpired | Clé expirée | Déclenché lorsqu’une clé est arrivée à expiration. |
| Microsoft.KeyVault.SecretNewVersionCreated | Nouvelle version du secret créée | Déclenché lors de la création d’un nouveau secret ou d’une nouvelle version de secret. |
| Microsoft.KeyVault.SecretNearExpiry | Expiration proche du secret | Déclenché lorsque la version actuelle d’un secret est sur le point d’expirer. (La valeur par défaut est 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.SecretExpired | Secret expiré | Déclenché lorsqu’un secret est arrivé à expiration. |

## <a name="event-examples"></a>Exemples d’événement

L’exemple suivant affiche le schéma pour **Microsoft.KeyVault.SecretNewVersionCreated** :

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| ---------- | ----------- |---|
| id | string | ID de l’objet qui a déclenché cet événement |
| VaultName | string | Nom du coffre de clés de l’objet qui a déclenché cet événement |
| objectType | string | Type de l’objet qui a déclenché cet événement |
| objectName | string | Nom de l’objet qui a déclenché cet événement |
| version | string | Version de l’objet qui a déclenché cet événement |
| nbf | nombre | Date not-before, en secondes, depuis 1970-01-01T00:00:00Z de l’objet qui a déclenché cet événement |
| exp | nombre | Date d’expiration, en secondes, depuis 1970-01-01T00:00:00Z de l’objet qui a déclenché cet événement |


## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour en savoir plus sur l’intégration de Key Vault à Event Grid, consultez [Supervision de Key Vault avec Azure Event Grid (préversion)](../key-vault/event-grid-overview.md).
* Pour un didacticiel sur l’intégration de Key Vault à Event Grid, consultez [Recevoir des notifications concernant un coffre de clés et y répondre avec Azure Event Grid (préversion)](../key-vault/event-grid-tutorial.md).
* Pour des conseils supplémentaires pour Key Vault et Azure Automation, consultez :
    - [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-overview.md)
    - [Monitoring de Key Vault avec Azure Event Grid (préversion)](../key-vault/event-grid-overview.md)
    - [Recevoir des notifications concernant un coffre de clés et y répondre avec Azure Event Grid (préversion)](../key-vault/event-grid-tutorial.md)
    - [Vue d’ensemble d’Azure Automation](../automation/index.yml)
