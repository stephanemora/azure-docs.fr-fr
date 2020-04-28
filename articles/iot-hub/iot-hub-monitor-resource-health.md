---
title: Surveiller l’intégrité de votre Azure IoT Hub | Microsoft Docs
description: Utilisez Azure Monitor et Azure Resource Health pour surveiller votre IoT Hub et diagnostiquer rapidement les problèmes
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.custom: amqp
ms.openlocfilehash: a1d74085090a3e20764d7b6fee84ffca52d5cb74
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732443"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes

Les entreprises qui implémentent Azure IoT Hub s’attendent à ce que leurs ressources fournissent des performances fiables. Pour vous aider à garder un œil attentif sur vos opérations, IoT Hub est entièrement intégré dans [Azure Monitor](../azure-monitor/index.yml) et [Azure Resource Health](../service-health/resource-health-overview.md). Ces deux services sont là pour vous fournir les données nécessaires au bon fonctionnement de vos solutions IoT.

Azure Monitor constitue une source unique pour la surveillance et la journalisation de tous vos services Azure. Vous pouvez envoyer les journaux de diagnostic générés par Azure Monitor aux journaux Azure Monitor, Event Hubs ou au stockage Azure pour un traitement personnalisé. Les paramètres des mesures et des diagnostics d’Azure Monitor vous permettent de visualiser les performances de vos ressources. Poursuivez la lecture de cet article pour savoir comment [utiliser Azure Monitor](#use-azure-monitor) avec votre IoT Hub. 

> [!IMPORTANT]
> Ni la fiabilité ni l’ordre des événements émis par le service IoT Hub à l’aide des journaux de diagnostic Azure Monitor ne sont garantis. Certains événements peuvent être perdus ou remis de manière désordonnée. De même, les journaux de diagnostic ne sont pas censés être en temps réel, et l’enregistrement des événements dans la destination de votre choix peut prendre plusieurs minutes.

Azure Resource Health vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources. Un tableau de bord indique l’état d’intégrité actuel et passé de chacun de vos IoT Hubs. Poursuivez à la section au bas de cet article pour savoir comment [utiliser Azure Resource Health](#use-azure-resource-health) avec votre IoT Hub. 

IoT Hub propose également ses propres métriques pour vous aider à comprendre l’état de vos ressources IoT. Pour plus d’informations, consultez [Comprendre les mesures IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Utiliser Azure Monitor

Azure Monitor fournit des informations de diagnostic pour les ressources Azure, ce qui vous permet de contrôler les opérations qui ont lieu au sein de votre IoT Hub.

Les paramètres de diagnostic d’Azure Monitor remplacent le moniteur d’opérations d’IoT Hub. Si vous utilisez actuellement la surveillance des opérations, vous devez migrer vos flux de travail. Pour plus d’informations, consultez [Migrer de la surveillance des opérations vers les paramètres de diagnostic](iot-hub-migrate-to-diagnostics-settings.md).

Pour en savoir plus sur les mesures et les événements spécifiques surveillés par Azure Monitor, consultez [Mesures prises en charge avec Azure Monitor](../azure-monitor/platform/metrics-supported.md) et [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Comprendre les journaux d’activité

Azure Monitor effectue le suivi des différentes opérations qui se produisent dans IoT Hub. Chaque catégorie dispose d’un schéma qui définit la façon dont les événements de cette catégorie sont signalés.

#### <a name="connections"></a>Connexions

La catégorie de connexions suit les événements de connexions et de déconnexion des appareils à partir d’un hub IoT, ainsi que les erreurs. Cette catégorie est utile pour identifier les tentatives de connexion non autorisées et/ou donner l’alerte lorsque vous êtes déconnecté des appareils.

> [!NOTE]
> Pour l’état de connexion fiable des appareils, voir [Pulsation des appareils](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Commandes cloud-à-appareil

La catégorie de commandes cloud-à-appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de messages cloud-à-appareil. Cette catégorie inclut les erreurs qui surviennent suite à ces événements :

* Envoi de messages cloud-à-appareil (erreurs liées aux expéditeurs non autorisés, par exemple)
* Réception de messages cloud-à-appareil (erreurs liées au dépassement du nombre de remises, par exemple)
* Réception de commentaires sur les messages cloud-à-appareil (erreurs liées à l’expiration des commentaires, par exemple)

Cette catégorie ne signale pas les erreurs lorsque le message cloud-à-appareil est bien remis mais qu’il n’est pas correctement traité par l’appareil.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Opérations d’identité des appareils

La catégorie d’opérations d’identité des appareils effectue le suivi des erreurs qui se produisent quand vous tentez de créer, mettre à jour ou supprimer une entrée dans le registre d’identité de votre hub IoT. Le suivi de cette catégorie est utile pour les scénarios d’approvisionnement.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Itinéraires

La catégorie de routage des messages assure le suivi des erreurs qui se produisent pendant l’évaluation du routage des messages et de l’intégrité du point de terminaison perçue par IoT Hub. Cette catégorie inclut les événements de type :

* Une règle est évaluée sur « indéfinie »
* Un point de terminaison est signalé comme étant inactif par IoT Hub
* Toutes les erreurs reçues depuis un point de terminaison 

Cette catégorie n’inclut pas les erreurs spécifiques aux messages eux-mêmes (les erreurs de limitation des appareils, par exemple) qui sont signalées dans la catégorie « télémétrie des appareils ».

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Télémétrie d’appareil

La catégorie de télémétrie d’appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de télémétrie. Cette catégorie inclut notamment les erreurs concernant l’envoi d’événements de télémétrie (par exemple, une limitation) et la réception des événements de télémétrie (par exemple, un lecteur non autorisé). Cette catégorie ne peut pas intercepter les erreurs provoquées par le code en cours d’exécution sur l’appareil lui-même.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Opérations de téléchargement de fichier

La catégorie de chargement de fichiers effectue le suivi des erreurs qui se produisent au niveau de l’IoT hub et qui sont liées à la fonctionnalité de chargement. Cette catégorie inclut :

* Erreurs qui se produisent avec l’URI SAP, par exemple en cas d’expiration avant qu’un appareil notifie le hub d’un téléchargement terminé.

* Échecs des téléchargements signalés par l’appareil.

* Erreurs qui se produisent lorsqu’un fichier est introuvable dans le stockage lors de la création du message de notification IoT Hub.

Cette catégorie ne peut pas détecter les erreurs qui surviennent directement pendant que l’appareil charge un fichier de stockage.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Opérations jumelles cloud à appareil

La catégorie des opérations jumelles cloud-à-appareil effectue le suivi des événements initiés par le service sur des jumeaux d’appareil. Ces opérations peuvent inclure : obtenir un jumeau, mettre à jour ou remplacer des balises, et mettre à jour ou remplacer les propriétés souhaitées.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Opérations jumelles appareil à cloud

La catégorie des opérations jumelles appareil-à-cloud effectue le suivi des événements initiés par l’appareil sur des jumeaux d’appareil. Ces opérations peuvent inclure l’obtention d’un jumeau, la mise à jour les propriétés signalées et l’abonnement aux propriétés souhaitées.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Requêtes de jumeaux

La catégorie des requêtes de jumeaux génère un rapport sur les demandes de requêtes pour jumeaux d’appareil lancées dans le cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Opérations de travaux

La catégorie des opérations de travaux génère un rapport sur les demandes de travaux pour mettre à jour des jumeaux d’appareil ou appeler des méthodes directes sur plusieurs appareils. Ces demandes sont lancées dans le cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Méthodes directes

La catégorie des méthodes directes assure le suivi des interactions demande-réponse envoyées à des appareils individuels. Ces demandes sont lancées dans le cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Traçage distribué (préversion)

La catégorie de traçage distribué suit les ID de corrélation pour les messages comportant l’en-tête de contexte de trace. Pour activer pleinement ces journaux d’activité, le code côté client doit être mis à jour en suivant [Analyser et diagnostiquer les applications IoT de bout en bout avec le traçage distribué IoT Hub (preview)](iot-hub-distributed-tracing.md).

Notez que `correlationId` est conforme à la proposition [Trace Context du W3](https://github.com/w3c/trace-context), car il contient un `trace-id` ainsi qu’un `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Journaux d’activité appareil à cloud (D2C) IoT Hub

IoT Hub enregistre ce journal en recevant un message contenant des propriétés de trace valides.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Ici, `durationMs` n’est pas calculé, car l’horloge IoT Hub peut ne pas être synchronisée avec l’horloge de l’appareil, ce qui fausse le calcul de durée. Nous recommandons d’écrire la logique en utilisant les horodatages de la section `properties` pour capturer les pics de latence de l’appareil vers le cloud.

| Propriété | Type | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Taille du message appareil-à-cloud, en octets |
| **deviceId** | Chaîne de caractères alphanumériques ASCII 7 bits | Identité de l’appareil |
| **callerLocalTimeUtc** | Horodateur UTC | Heure de création du message telle que rapportée par l’horloge local de l’appareil |
| **calleeLocalTimeUtc** | Horodateur UTC | Heure de réception du message sur la passerelle d’IoT Hub telle qu’elle est rapportée par l’horloge du service IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Journaux d’activité d’entrée IoT Hub

IoT Hub enregistre ce journal lorsqu’un message contenant des propriétés de trace valides écrit dans l’Event Hub interne ou intégré.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Dans la section `properties`, ce journal contient des informations supplémentaires sur l’entrée des messages.

| Propriété | Type | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Indique si le routage des messages est activé (true) ou non (false) dans le hub IoT |
| **parentSpanId** | String | [span-id](https://w3c.github.io/trace-context/#parent-id) du message parent, qui serait la trace du message D2C dans ce cas-ci |

##### <a name="iot-hub-egress-logs"></a>Journaux d’activité de sortie IoT Hub

IoT Hub enregistre ce journal lorsque le [routage](iot-hub-devguide-messages-d2c.md) est activé et que le message est écrit sur un [point de terminaison](iot-hub-devguide-endpoints.md). Si le routage n’est pas activé, IoT Hub n’enregistre pas ce journal.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Dans la section `properties`, ce journal contient des informations supplémentaires sur l’entrée des messages.

| Propriété | Type | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Nom du point de terminaison de routage |
| **endpointType** | String | Type du point de terminaison de routage |
| **parentSpanId** | String | [span-id](https://w3c.github.io/trace-context/#parent-id) du message parent, qui serait la trace du message d’entrée IoT Hub dans ce cas-ci |

#### <a name="configurations"></a>Configurations

La configuration d’IoT Hub suit et enregistre les événements et les erreurs pour l’ensemble de fonctionnalités de la gestion automatique des appareils.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Flux d’appareils (préversion)

La catégorie des flux d’appareil assure le suivi des interactions requête-réponse envoyées à des appareils individuels.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Lecture de journaux d’activité à partir d’Azure Event Hubs

Après avoir configuré la journalisation des événements via les paramètres de diagnostic, vous pouvez créer des applications qui lisent les journaux d’activité, pour vous permettre d’agir en fonction des informations qu’ils contiennent. Cet exemple de code extrait les journaux d’activité à partir d’un concentrateur d’événements :

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Utilisation d’Azure Resource Health

Utilisez Azure Resource Health pour vérifier si votre IoT Hub est opérationnel. Vous pouvez également vérifier si une panne régionale affecte l’intégrité de votre IoT Hub. Pour obtenir des détails spécifiques sur l’état d’intégrité de votre Azure IoT Hub, nous vous recommandons d[’utiliser Azure Monitor](#use-azure-monitor).

Azure IoT Hub indique l’intégrité au niveau régional. Si une panne régionale affecte votre IoT Hub, l’état d’intégrité prend l’état **Inconnu**. Pour en savoir plus, consultez l’article [Types de ressources et contrôles d’intégrité dans Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Pour vérifier l’intégrité de vos IoT Hubs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez à **Service Health** > **Resource Health**.

3. Dans les champs déroulants, sélectionnez votre abonnement, puis **IoT Hub** comme type de ressource.

Pour en savoir plus sur la façon d’interpréter les données d’intégrité, consultez [Vue d’ensemble d’Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les mesures IoT Hub](iot-hub-metrics.md)
* [Surveillance à distance IoT et notifications avec Azure Logic Apps connectant votre IoT Hub et votre boîte aux lettres](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
