---
title: Azure Kubernetes Service comme source Event Grid (préversion)
description: Cet article explique comment utiliser Azure Kubernetes Service comme source d’événement Event Grid. Il fournit le schéma et des liens vers des articles de procédure et des tutoriels.
author: zr-msft
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: 16b82ea59e69457475966fecfb8a0ca8d7dd20ce
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733949"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>Azure Kubernetes Service (AKS) comme source Event Grid (préversion)

Cet article fournit les propriétés et le schéma pour les événements AKS.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). Cet article fournit également une liste de guides de démarrage rapide et de tutoriels permettant d’utiliser AKS comme source d’événement.

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>Types d’événement disponibles

AKS émet les types d’événements suivants :

|    Type d'événement                                             |    Description                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | Déclenché quand la liste des versions de Kubernetes disponibles est mise à jour. |

## <a name="properties-common-to-all-events"></a>Propriétés communes à tous les événements

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)
Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement.
Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement. Chaque événement contient les données générales suivantes :

|     Propriété          |     Type     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur.                                      |
|    `subject`            |    string    |    Chemin de l’objet de l’événement, défini par le serveur de publication.                                                                                              |
|    `eventType`          |    string    |    Un des types d’événements inscrits pour cette source d’événement.                                                                                  |
|    `eventTime`          |    string    |    Heure à laquelle l’événement est généré selon l’heure UTC du fournisseur.                                                                         |
|    `id`                 |    string    |    Identificateur unique de l’événement.                                                                                                            |
|    `data`               |    object    |    Données d’événement de stockage Blob.                                                                                                                    |
|    `dataVersion`        |    string    |    Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma.                                                          |
|    `metadataVersion`    |    string    |    Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur.    |

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement.
Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement. Chaque événement contient les données générales suivantes :

|     Propriété          |     Type     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur.                                      |
|    `subject`            |    string    |    Chemin de l’objet de l’événement, défini par le serveur de publication.                                                                                              |
|    `type`          |    string    |    Un des types d’événements inscrits pour cette source d’événement.                                                                                  |
|    `time`          |    string    |    Heure à laquelle l’événement est généré selon l’heure UTC du fournisseur.                                                                         |
|    `id`                 |    string    |    Identificateur unique de l’événement.                                                                                                            |
|    `data`               |    object    |    Données d’événement de stockage Blob.                                                                                                                    |
| `specversion` | string | Version de la spécification de schéma CloudEvents. |

---

## <a name="example-events"></a>Exemples d'événements

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```
# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```

---

L’objet de données contient les propriétés suivantes :

|    Propriété                        | Type   | Description                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | string | Dernière version prise en charge de Kubernetes disponible.        |
| `latestStableKubernetesVersion`    | string | Dernière version prise en charge stable de Kubernetes disponible. |
| `lowestMinorKubernetesVersion`     | string | Version prise en charge la plus ancienne de Kubernetes disponible.        |
| `latestPreviewKubernetesVersion`   | string | Préversion la plus ancienne de Kubernetes disponible.          |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md)