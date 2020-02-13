---
title: Configurer des journaux de diagnostic - Azure Event Hubs | Microsoft Docs
description: Découvrez comment configurer les journaux d’activité et de diagnostic pour Event Hubs dans Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162308"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurer les journaux de diagnostic pour un Event Hub Azure.

Vous pouvez afficher deux types de journaux d’activité pour Azure Event Hubs :

* **[Journaux d’activité](../azure-monitor/platform/platform-logs-overview.md)** : Ces journaux d’activité comportent des informations sur les opérations effectuées sur un travail. Les journaux d’activité sont toujours activés.
* **[Journaux de diagnostic](../azure-monitor/platform/platform-logs-overview.md)** : Vous pouvez configurer les journaux de diagnostic pour obtenir des informations plus détaillées sur tous les événements associés à un travail. Les journaux de diagnostic couvrent les activités qui se déroulent entre la création du travail et sa suppression, notamment les mises à jour et les activités durant l’exécution du travail.

## <a name="enable-diagnostic-logs"></a>Activer les journaux de diagnostic

Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, effectuez les étapes suivantes :

1.  Dans le [portail Azure](https://portal.azure.com), sous **Supervision + gestion**, cliquez sur **Journaux de diagnostic**.

    ![Navigation dans le volet jusqu’aux journaux de diagnostic](./media/event-hubs-diagnostic-logs/image1.png)

2.  Cliquez sur la ressource que vous souhaitez surveiller.

3.  Cliquez sur **Activer les diagnostics**.

    ![Activer les journaux de diagnostic](./media/event-hubs-diagnostic-logs/image2.png)

4.  Pour l’**état**, cliquez sur **ACTIVÉ**.

    ![Modifier l’état des journaux de diagnostic](./media/event-hubs-diagnostic-logs/image3.png)

5.  Définissez la cible d’archivage de votre choix, par exemple un compte de stockage, un hub d’événements ou Journaux Azure Monitor.

6.  Enregistrez les nouveaux paramètres de diagnostic.

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux d’activité apparaissent dans la cible d’archivage configurée, dans le volet **Journaux de diagnostic**.

Pour plus d’informations sur la configuration des diagnostics, consultez la [vue d’ensemble des journaux de diagnostic Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Catégories de journaux de diagnostic

Event Hubs capture les journaux de diagnostic pour deux catégories :

* **Journaux d’activité d’archivage** : journaux d’activité liés aux archives Event Hubs, en particulier aux erreurs d’archivage.
* **Journaux d’activité des opérations** : informations relatives à ce qui se passe pendant les opérations Event Hubs, en particulier le type d’opération tel que la création d’un hub d’événements, les ressources utilisées et l’état de l’opération.

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux d’activité sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne au format décrit dans les sections suivantes.

### <a name="archive-logs-schema"></a>Schéma des journaux d’activité d’archivage

Les chaînes JSON du journal d’archivage incluent les éléments listés dans le tableau suivant :

Name | Description
------- | -------
TaskName | Description de la tâche en échec.
ActivityId | ID interne, utilisé à des fins de suivi.
trackingId | ID interne, utilisé à des fins de suivi.
resourceId | ID de ressource Azure Resource Manager.
eventHub | Nom complet de l’Event Hub (nom d’espace de noms inclus).
partitionId | Partition Event Hub sur laquelle s’effectue l’opération en écriture.
archiveStep | ArchiveFlushWriter
startTime | Heure de début de la défaillance.
failures | Nombre d’occurrences d’une défaillance.
durationInSeconds | Durée de la défaillance.
message | Message d’erreur.
catégorie | ArchiveLogs

Le code suivant est un exemple de chaîne JSON de journal d’archivage :

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Schéma des journaux d’activité des opérations

Les chaînes JSON du journal des opérations incluent les éléments répertoriés dans le tableau suivant :

Name | Description
------- | -------
ActivityId | ID interne, utilisé à des fins de suivi.
EventName | Nom d’opération.  
resourceId | ID de ressource Azure Resource Manager.
SubscriptionId | l'ID d'abonnement.
EventTimeString | Durée de l’opération.
EventProperties | Propriétés de l’opération.
Statut | État de l’opération.
Appelant | Appelant de l’opération (portail Azure ou client de gestion).
catégorie | OperationalLogs

Le code suivant est un exemple de chaîne JSON de journal des opérations :

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Présentation d’Event Hubs](event-hubs-what-is-event-hubs.md)
- [Vue d’ensemble de l'API Event Hubs](event-hubs-api-overview.md)
- Prise en main des hubs d’événements
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
