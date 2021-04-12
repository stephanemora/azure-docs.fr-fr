---
title: Azure Key Vault en tant que source Event Grid
description: Décrit les propriétés et le schéma qui sont fournis pour les événements Azure Key Vault avec Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363404"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault en tant que source Event Grid

Cet article fournit les propriétés et le schéma des événements dans [Azure Key Vault](../key-vault/index.yml). Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Types d’événement disponibles

Un compte Azure Key Vault génère les types d’événements suivants :

| Nom complet de l’événement | Nom complet de l’événement | Description |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Nouvelle version du certificat créée | Déclenché lors de la création d’un nouveau certificat ou d’une nouvelle version de certificat. |
| Microsoft.KeyVault.CertificateNearExpiry | Expiration proche du certificat | Déclenché lorsque la version actuelle du certificat arrive à expiration. (L’événement est déclenché 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.CertificateExpired | Le certificat a expiré | Déclenché lorsque le certificat a expiré. |
| Microsoft.KeyVault.KeyNewVersionCreated | Nouvelle version de la clé créée | Déclenché lors de la création d’une nouvelle clé ou version de clé. |
| Microsoft.KeyVault.KeyNearExpiry | Expiration proche de la clé | Déclenché lorsque la version actuelle d’une clé va expirer. (L’événement est déclenché 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.KeyExpired | Clé expirée | Déclenché lorsqu’une clé est arrivée à expiration. |
| Microsoft.KeyVault.SecretNewVersionCreated | Nouvelle version du secret créée | Déclenché lors de la création d’un nouveau secret ou d’une nouvelle version de secret. |
| Microsoft.KeyVault.SecretNearExpiry | Expiration proche du secret | Déclenché lorsque la version actuelle d’un secret est sur le point d’expirer. (L’événement est déclenché 30 jours avant la date d’expiration.) |
| Microsoft.KeyVault.SecretExpired | Secret expiré | Déclenché lorsqu’un secret est arrivé à expiration. |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Modification de la stratégie d’accès au coffre | Déclenché lors de la modification d’une stratégie d’accès sur Key Vault. Il comprend un scénario dans lequel le modèle d’autorisation de Key Vault est modifié vers/depuis le contrôle d’accès en fonction du rôle Azure.   |

## <a name="event-examples"></a>Exemples d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

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

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

L’exemple suivant affiche le schéma pour **Microsoft.KeyVault.SecretNewVersionCreated** :

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Propriétés d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)
Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `topic` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données de l’événement App Configuration. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |


# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `source` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| `type` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `time` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données de l’événement App Configuration. |
| `specversion` | string | Version de la spécification de schéma CloudEvents. |

---
 

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| ---------- | ----------- |---|
| `id` | string | ID de l’objet qui a déclenché cet événement |
| `vaultName` | string | Nom du coffre de clés de l’objet qui a déclenché cet événement |
| `objectType` | string | Type de l’objet qui a déclenché cet événement |
| `objectName` | string | Nom de l’objet qui a déclenché cet événement |
| `version` | string | Version de l’objet qui a déclenché cet événement |
| `nbf` | nombre | Date not-before, en secondes, depuis 1970-01-01T00:00:00Z de l’objet qui a déclenché cet événement |
| `exp` | nombre | Date d’expiration, en secondes, depuis 1970-01-01T00:00:00Z de l’objet qui a déclenché cet événement |

## <a name="tutorials-and-how-tos"></a>Tutoriels et articles de procédures
|Intitulé  |Description  |
|---------|---------|
| [Monitorage d’événements Key Vault avec Azure Event Grid](../key-vault/general/event-grid-overview.md) | Vue d’ensemble de l’intégration de Key Vault avec Event Grid. |
| [Tutoriel : Créer des événements Key Vault avec Event Grid et en effectuer le monitorage](../key-vault/general/event-grid-logicapps.md) | Découvrez comment configurer des notifications Event Grid pour Key Vault. |


## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../key-vault/general/overview.md).

