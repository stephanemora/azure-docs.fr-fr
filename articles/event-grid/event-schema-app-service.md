---
title: Azure App Service en tant que source Event Grid
description: Cet article explique comment utiliser Azure App Service en tant que source d’événement Event Grid. Il fournit le schéma et des liens vers des articles de procédure et des tutoriels.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: jafreebe
ms.openlocfilehash: 224cb44ef7293f47855b5b418830a7fc4bf5ecd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366651"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service en tant que source Event Grid

Cet article décrit les propriétés et le schéma des événements Azure App Service. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). Cet article fournit également une liste de démarrages rapides et de tutoriels permettant d’utiliser Azure App Service comme source d’événement.

## <a name="available-event-types"></a>Types d’événement disponibles

Azure App Service émet les types d’événements suivants :

|    Type d'événement                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Déclenché lorsqu’une sauvegarde a démarré                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Déclenché lorsqu’une sauvegarde est terminée                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Déclenché lorsqu’une sauvegarde a échoué                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Déclenché lorsqu’une restauration à partir d’une sauvegarde a démarré        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Déclenché lorsqu’une restauration à partir d’une sauvegarde est terminée      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Déclenché lorsqu’une restauration à partir d’une sauvegarde a échoué         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Déclenché lorsqu’un échange d’emplacement a démarré                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Déclenché lorsqu’un échange d’emplacement est terminé                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Déclenché lorsqu’un échange d’emplacement a échoué                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Déclenché lorsqu’un échange d’emplacement avec aperçu a démarré           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Déclenché lorsqu’un échange d’emplacement avec aperçu a été annulé    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Déclenché lorsqu’un site a été redémarré                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Déclenché lorsqu’un site a été arrêté                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Déclenché lorsque les paramètres d’application d’un site ont été modifiés             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Déclenché lorsqu’un plan App Service est mis à jour                 |

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

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

L’objet de données contient les propriétés suivantes :

|    Propriété                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Détail de l’action sur l’application                                                                                       |
|    `action`                  |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                    |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`         |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`    |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|    `requestId`               |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                 |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                    |    string    |    Verbe HTTP de cette opération                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

L’objet de données contient les propriétés suivantes :

|    Propriété                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Détail de l’action sur l’application                                                                                       |
|    `action`                  |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                    |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`         |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`    |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|    `requestId`               |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                 |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                    |    string    |    Verbe HTTP de cette opération                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

L’objet de données contient les propriétés suivantes :

|    Propriété                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Détail de l’action sur l’application                                                                                       |
|    `action`                 |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                    |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`         |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`    |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|   `requestId`               |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                 |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                    |    string    |    Verbe HTTP de cette opération                                                                                       |
|    `sourceSlot`              |    string    |    Emplacement source de l’échange                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

L’objet de données contient les propriétés suivantes :

|    Propriété                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Détail de l’action sur l’application                                                                                       |
|    `action`                 |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                    |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`         |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`    |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|    `requestId`               |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                 |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                    |    string    |    Verbe HTTP de cette opération                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

L’objet de données comporte les propriétés suivantes :

|    Propriété                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Détail de l’action sur l’application                                                                                       |
|    `action`                  |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                    |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`         |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`    |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|    `requestId`               |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                 |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                    |    string    |    Verbe HTTP de cette opération                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

L’objet de données comporte les propriétés suivantes :

|    Propriété                         |    Type      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    Détail de l’action sur le plan App Service                                                                          |
|    `stampKind`                        |    string    |    Type d’environnement dans lequel se trouve le plan App Service                                                                     |
|    `action`                           |    string    |    Type d’action sur le plan App Service                                                                            |
|    `status`                           |    string    |    État de l’opération sur le plan App Service                                                                   |
|    `sku`                              |    object    |    Référence SKU du plan App Service                                                                                       |
|    `name`                             |    string    |    Nom du plan App Service                                                                                      |
|    `Tier`                             |    string    |    Niveau de service du plan App Service                                                                                      |
|    `Size`                             |    string    |    Taille du plan App Service                                                                                      |
|    `Family`                           |    string    |    Famille du plan App Service                                                                                        |
|    `Capacity`                         |    string    |    Capacité du plan App Service                                                                                      |
|    `action`                           |    string    |    Type d’action de l’opération                                                                                   |
|    `name`                             |    string    |    Nom du site web qui a organisé cet événement                                                                          |
|    `clientRequestId`                  |    string    |    ID de demande du client généré par le service d'application pour l'opération API du site qui a déclenché cet événement         |
|    `correlationRequestId`             |    string    |    ID de demande de corrélation généré par le service d'application pour l'opération API du site qui a déclenché cet événement    |
|    `requestId`                        |    string    |    ID de demande généré par le service d'application pour l'opération API du site qui a déclenché cet événement                |
|    `address`                         |    string    |    URL de la requête HTTP de cette opération                                                                                |
|    `verb`                             |    string    |    Verbe HTTP de cette opération                                                                                       |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md)