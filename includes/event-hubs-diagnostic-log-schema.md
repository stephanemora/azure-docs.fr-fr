---
title: Fichier include
description: Fichier include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 06/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: aa3c541db838f9b33208dba7ba9ac33422d3b1a5
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060797"
---
Event Hubs capture les journaux de diagnostic pour les catégories suivantes :

| Category | Description | 
| -------- | ----------- | 
| Journaux d’activité d’archivage | Capture des informations sur les opérations [Event Hubs Capture](../articles/event-hubs/event-hubs-capture-overview.md), en particulier les journaux relatifs aux erreurs de capture. |
| Journaux d’activité des opérations | Capturent toutes les opérations de gestion qui sont effectuées sur l’espace de noms Azure Event Hubs. Les opérations de données ne sont pas capturées en raison du volume élevé d'opérations de données effectuées sur Azure Event Hubs. |
| Journaux de mise à l’échelle automatique | Capture les opérations d’augmentation automatique effectuées sur un espace de noms Event Hubs. |
| Journaux du coordinateur Kafka | Capture les opérations du coordinateur Kafka relatives à Event Hubs. |
| Journaux des erreurs d’utilisateur Kafka | Capture des informations sur les API Kafka appelées sur Event Hubs. |
| Événement de connexion au réseau virtuel Event Hubs | Capture des informations sur les adresses IP et les réseaux virtuels envoyant du trafic vers Event Hubs. |
| Journaux utilisateur des clés gérées par le client | Capture les opérations relatives à la clé gérée par le client. |


Tous les journaux d’activité sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne au format décrit dans les sections suivantes.

### <a name="archive-logs-schema"></a>Schéma des journaux d’activité d’archivage

Les chaînes JSON du journal d’archivage incluent les éléments listés dans le tableau suivant :

Nom | Description
------- | -------
`TaskName` | Description de la tâche ayant échoué
`ActivityId` | ID interne, utilisé à des fins de suivi
`trackingId` | ID interne, utilisé à des fins de suivi
`resourceId` | ID de ressource Azure Resource Manager
`eventHub` | Nom complet de l’Event Hub (nom d’espace de noms inclus)
`partitionId` | Partition Event Hub sur laquelle s’effectue l’opération en écriture
`archiveStep` | Valeurs possibles : ArchiveFlushWriter, DestinationInit
`startTime` | Heure de début de la défaillance
`failures` | Nombre d’occurrences de l’échec
`durationInSeconds` | Durée de la défaillance
`message` | Message d'erreur
`category` | ArchiveLogs

Le code suivant est un exemple de chaîne JSON de journal d’archivage :

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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

Nom | Description
------- | -------
`ActivityId` | ID interne, utilisé à des fins de suivi |
`EventName` | Nom d’opération. Pour obtenir la liste des valeurs de cet élément, consultez [Noms des événements](#event-names) |
`resourceId` | ID de ressource Azure Resource Manager |
`SubscriptionId` | Identifiant d’abonnement |
`EventTimeString` | Durée de l’opération |
`EventProperties` |Propriétés de l'opération. Cet élément fournit des informations complémentaires sur l'événement, comme illustré dans l'exemple suivant. |
`Status` | État de l’opération. La valeur peut être **Opération réussie** ou **Échec**.  |
`Caller` | Appelant de l’opération (portail Azure ou client de gestion) |
`Category` | OperationalLogs |

Le code suivant est un exemple de chaîne JSON de journal des opérations :

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

#### <a name="event-names"></a>Noms des événements
Le nom de l'événement est renseigné sous la forme « type d'opération + type de ressource » à partir des énumérations suivantes. Par exemple, `Create Queue`, `Retrieve Event Hu` ou `Delete Rule`. 

| Type d'opération | Type de ressource | 
| -------------- | ------------- | 
| <ul><li>Créer</li><li>Update</li><li>Supprimer</li><li>Récupération</li><li>Unknown</li></ul> | <ul><li>Espace de noms</li><li>File d'attente</li><li>Rubrique</li><li>Abonnement</li><li>Event Hub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Règle</li>ConsumerGroup</li> |

### <a name="autoscale-logs-schema"></a>Schéma des journaux de mise à l’échelle automatique
Le code JSON des journaux de mise à l’échelle automatique inclut les éléments listés dans le tableau suivant :

| Nom | Description |
| ---- | ----------- | 
| `TrackingId` | ID interne, utilisé à des fins de suivi |
| `ResourceId` | ID de ressource Azure Resource Manager. |
| `Message` | Message d’information, qui fournit des détails sur l’action d’augmentation automatique. Le message contient les valeurs précédente et actuelle de l’unité de débit pour un espace de noms donné et indique ce qui a déclenché l’augmentation de l’unité de débit. |

Voici un exemple d’événement de mise à l’échelle automatique : 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

### <a name="kafka-coordinator-logs-schema"></a>Schéma des journaux du coordinateur Kafka
Le code JSON des journaux du coordinateur Kafka inclut les éléments listés dans le tableau suivant :

| Nom | Description |
| ---- | ----------- | 
| `RequestId` | ID de la demande, utilisé à des fins de suivi |
| `ResourceId` | ID de ressource Azure Resource Manager |
| `Operation` | Nom de l’opération effectuée au cours de la coordination du groupe |
| `ClientId` | ID client |
| `NamespaceName` | Nom de l’espace de noms | 
| `SubscriptionId` | ID d’abonnement Azure |
| `Message` | Message d’information ou d’avertissement, qui fournit des détails sur les actions effectuées pendant la coordination du groupe |

#### <a name="example"></a>Exemple

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

### <a name="kafka-user-error-logs-schema"></a>Schéma des journaux des erreurs d’utilisateur Kafka
Le code JSON des journaux des erreurs d’utilisateur Kafka inclut les éléments listés dans le tableau suivant :

| Nom | Description |
| ---- | ----------- |
| `TrackingId` | ID de suivi, utilisé à des fins de suivi |
| `NamespaceName` | Nom de l’espace de noms |
| `Eventhub` | Nom du hub d’événements |
| `PartitionId` | ID de partition (Partition ID) |
| `GroupId` | ID de groupe |
| `ClientId` | ID client |
| `ResourceId` | ID de ressource Azure Resource Manager. |
| `Message` | Message d’information, qui fournit des détails sur une erreur |

### <a name="event-hubs-virtual-network-connection-event-schema"></a>Schéma de l’événement de connexion de réseau virtuel Event Hubs
Le code JSON de l’événement de connexion de réseau virtuel Event Hubs comprend les éléments listés dans le tableau suivant :

| Nom | Description |
| ---  | ----------- | 
| `SubscriptionId` | ID d’abonnement Azure |
| `NamespaceName` | Nom de l’espace de noms |
| `IPAddress` | Adresse IP d’un client se connectant au service Event Hubs |
| `Action` | Action effectuée par le service Event Hubs lors de l’évaluation des demandes de connexion. Les actions prises en charge sont **Accepter la connexion** et **Refuser la connexion**. |
| `Reason` | Fournit une raison pour laquelle l’action a été effectuée |
| `Count` | Nombre d’occurrences de l’action donnée |
| `ResourceId` | ID de ressource Azure Resource Manager. |

Les journaux de réseau virtuel ne sont générés que si l’espace de noms autorise l’accès provenant des **réseaux sélectionnés** ou **d’adresses IP spécifiques** (règles de filtre d’adresse IP). Si vous souhaitez obtenir des journaux de réseau virtuel pour suivre l'adresse IP des clients qui se connectent à l'espace de noms Event Hubs sans pour autant restreindre l'accès à votre espace de noms à l'aide de ces fonctionnalités, vous pouvez appliquer la solution de contournement suivante : [Activez le filtrage IP](../articles/event-hubs/event-hubs-ip-filtering.md) et ajoutez la plage IPv4 adressable totale (1.0.0.0/1-255.0.0.0/1). Le filtrage IP Event Hubs ne prend pas en charge les plages IPv6. Notez que vous pouvez voir des adresses de point de terminaison privées au format IPv6 dans le journal. 

#### <a name="example"></a>Exemple

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="customer-managed-key-user-logs-schema"></a>Schéma des journaux utilisateur des clés gérées par le client
Le code JSON des journaux utilisateur des clés gérées par le client inclut les éléments listés dans le tableau suivant :

| Nom | Description |
| ---- | ----------- | 
| `Category` | Type de catégorie d’un message. Il correspond à l’une des valeurs suivantes : **error** et **info** |
| `ResourceId` | ID de ressource interne, qui contient l’ID d’abonnement Azure et le nom de l’espace de noms |
| `KeyVault` | Nom de la ressource Key Vault |
| `Key` | Nom de la clé Key Vault. |
| `Version` | Version de la clé Key Vault |
| `Operation` | Nom d’une opération effectuée pour traiter les demandes |
| `Code` | Code d'état |
| `Message` | Message qui fournit des détails sur une erreur ou des informations |