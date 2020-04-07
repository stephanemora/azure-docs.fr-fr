---
title: Mesures de Stockage Azure dans Azure Monitor | Microsoft Docs
description: Découvrez les nouvelles mesures proposées par Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247093"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Mesures de stockage Azure dans Azure Monitor

Grâce aux mesures de Stockage Azure, vous pouvez analyser les tendances d’utilisation, suivre les demandes et diagnostiquer les problèmes liés à votre compte de stockage.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre différents services Azure. Pour plus d’informations, voir [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Stockage Azure s’intègre à Azure Monitor en envoyant des données de mesure à la plateforme Azure Monitor.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez y accéder à partir du [Portail Azure](https://portal.azure.com), des API d’Azure Monitor (REST et .NET) et des solutions d’analyse comme Event Hubs. Pour plus d’informations, voir [Mesures Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Les mesures sont activées par défaut et vous pouvez accéder aux 93 derniers jours de données. Si vous souhaitez conserver des données sur une période plus longue, vous pouvez archiver les données de mesures dans un compte de stockage Azure. Celui-ci est configuré dans les [paramètres de diagnostic](../../azure-monitor/platform/platform-logs-overview.md) dans Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Accéder aux mesures dans le portail Azure

Vous pouvez surveiller les mesures au fil du temps dans le portail Azure. L’exemple suivant montre comment afficher les **Transactions** au niveau du compte.

![capture d’écran d’accès aux mesures dans le portail Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Pour les métriques prenant en charge des dimensions, vous pouvez les filtrer avec la valeur de dimension souhaitée. L’exemple suivant montre comment afficher **Transactions** au niveau du compte sur une opération spécifique en sélectionnant des valeurs pour la dimension **Nom de l’API**.

![capture d’écran d’accès aux mesures avec une dimension dans le portail Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Accéder aux mesures avec l’API REST

Azure Monitor fournit des [API REST](/rest/api/monitor/) pour les définitions et valeurs de mesures. Cette section vous montre comment lire les mesures de stockage. L’ID de ressource est utilisé dans toutes les API REST. Pour plus d’informations, consultez Compréhension de l’ID de ressource pour des services dans Storage.

L’exemple suivant montre comment utiliser [ArmClient](https://github.com/projectkudu/ARMClient) sur la ligne de commande pour simplifier les tests avec l’API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Répertorier les définitions de mesures au niveau du compte avec l’API REST

L’exemple suivant montre comment répertorier les définition de mesures au niveau du compte :

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Si vous souhaitez répertorier les définitions de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

La réponse contient la définition de mesure au format JSON :

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Lire les valeurs de mesures au niveau du compte avec l’API REST

L’exemple suivant montre comment lire les données de mesures au niveau du compte :

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Dans l’exemple ci-dessus, si vous souhaitez lire des valeurs de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

La réponse suivante contient des valeurs de mesures au format JSON :

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Accéder aux mesures avec le kit de développement logiciel (SDK) .NET

Azure Monitor fournit des [kits de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pour lire des définitions et valeurs de mesures. L’[exemple de code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) montre comment utiliser le kit de développement logiciel (SDK) avec des paramètres différents. Vous devez utiliser `0.18.0-preview` ou version ultérieure pour les mesures de stockage. L’ID de ressource est utilisé dans le kit de développement logiciel (SDK) .NET. Pour plus d’informations, consultez Compréhension de l’ID de ressource pour des services dans Storage.

L’exemple suivant montre comment utiliser le kit de développement logiciel (SDK) Azure Monitor .NET pour lire les mesures de stockage.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Répertorier les définitions de mesures au niveau du compte avec le kit de développement logiciel (SDK) .NET

L’exemple suivant montre comment répertorier les définition de mesures au niveau du compte :

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Si vous souhaitez répertorier les définitions de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Lire les valeurs de mesure avec le kit de développement logiciel .NET

L’exemple suivant montre comment lire des données `UsedCapacity` au niveau du compte :

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Dans l’exemple ci-dessus, si vous souhaitez lire des valeurs de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Lire les valeurs de mesures multidimensionnelles avec le kit de développement logiciel (SDK) .NET

Pour les mesures multidimensionnelles, vous devez définir le filtre des métadonnées si vous souhaitez lire les données métriques sur la valeur de dimension spécifique.

L’exemple suivant montre comment lire les données de mesures sur la métrique prenant en charge plusieurs dimensions :

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Présentation des ID de ressources pour des services dans Stockage Azure

L’ID de ressource est l’identificateur unique d’une ressource dans Azure. Lorsque vous utilisez l’API REST Azure Monitor pour lire des définitions ou valeurs de mesures, vous devez utiliser l’ID de ressource de la ressource sur laquelle vous envisagez de travailler. Le format du modèle d’ID de ressource est le suivant :

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage fournit des mesures au niveau du compte de stockage et au niveau du service avec Azure Monitor. Par exemple, vous pouvez récupérer des mesures pour le stockage d’objets blob uniquement. Chaque niveau possède un ID de ressource propre, qui est utilisé pour récupérer les mesures pour ce niveau uniquement.

### <a name="resource-id-for-a-storage-account"></a>ID de ressource d’un compte de stockage

Le format pour spécifier l’ID de ressource d’un compte de stockage est le suivant.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID de ressource pour les services de stockage

Le format pour spécifier l’ID de ressource de chacun des services de stockage est le suivant.

* ID de ressource de service Blob
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID de ressource de service de Table
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID de ressource de service de File d’attente
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID de ressource de service de fichier
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de ressource dans l’API REST Azure Monitor

Le modèle utilisé lors de l’appel de l’API REST Azure Monitor est le suivant.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Métriques de capacité
Les valeurs de mesures de capacité sont envoyées à Azure Monitor toutes les heures. Les valeurs sont actualisées tous les jours. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de mesures sont présentées. Le fragment de temps pris en charge pour toutes les mesures de capacité est d’une heure (PT1H).

Stockage Azure fournit les mesures de capacité suivantes dans Azure Monitor.

### <a name="account-level"></a>Niveau du compte

| Nom de métrique | Description |
| ------------------- | ----------------- |
| UsedCapacity | Quantité de stockage utilisée par le compte de stockage. Pour les comptes de stockage standard, il s’agit de la somme de la capacité utilisée par les objets blob, tables, fichiers et files d’attente. Pour les comptes de stockage Premium et les comptes de stockage Blob, elle équivaut à BlobCapacity. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="blob-storage"></a>Stockage d'objets blob

| Nom de métrique | Description |
| ------------------- | ----------------- |
| BlobCapacity | Total de stockage d’objets blob utilisé dans le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 <br/> Dimensions : **BlobType** et **BlobTier** ([définition](#metrics-dimensions)) |
| BlobCount    | Nombre d’objets blob stockés dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 <br/> Dimensions : **BlobType** et **BlobTier** ([définition](#metrics-dimensions)) |
| ContainerCount    | Nombre de conteneurs dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| IndexCapacity     | Espace utilisé par l'index hiérarchique d'ADLS Gen2 <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="table-storage"></a>Stockage de tables

| Nom de métrique | Description |
| ------------------- | ----------------- |
| TableCapacity | Quantité de stockage de tables utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| TableCount   | Nombre de tables dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| TableEntityCount | Nombre d’entités de table dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="queue-storage"></a>Stockage de files d'attente

| Nom de métrique | Description |
| ------------------- | ----------------- |
| QueueCapacity | Quantité de stockage de files d’attente utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueCount   | Nombre de files d’attente dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueMessageCount | Nombre de messages de file d’attente non expirés dans le compte de stockage. <br/><br/>Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="file-storage"></a>Stockage Fichier

| Nom de métrique | Description |
| ------------------- | ----------------- |
| FileCapacity | Quantité de stockage de fichiers utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| FileCount   | Nombre de fichiers dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| FileShareCount | Nombre de partages de fichiers dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

## <a name="transaction-metrics"></a>Métriques de transaction

Des métriques de transaction sont émises lors de chacune des requêtes adressées à un compte de stockage entre Stockage Azure et Azure Monitor. En l'absence d'activité sur votre compte de stockage, il n'y aura pas de données sur les métriques de transaction au cours la période. Toutes les mesures de transaction sont disponibles au niveau du compte et au niveau du service (stockage d’objets blob, stockage de tables, fichiers Azure et stockage de files d’attente). Le fragment de temps définit l’intervalle de temps auquel des valeurs de mesures sont présentées. Les fragments de temps pris en charge pour toutes les mesures de transaction sont PT1H et PT1M.

Stockage Azure fournit les mesures de transaction suivantes dans Azure Monitor.

| Nom de métrique | Description |
| ------------------- | ----------------- |
| Transactions | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. <br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimensions applicables : ResponseType, GeoType, ApiName et Authentication ([Définition](#metrics-dimensions))<br/> Exemple de valeur : 1 024 |
| Entrée | Quantité de données d’entrée. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Sortie | Quantité de données de sortie. Ce nombre inclut les sorties vers un client externe à partir de Stockage Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessServerLatency | Durée moyenne utilisée pour traiter une requête réussie par Stockage Azure. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessE2ELatency | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Disponibilité | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris les requêtes qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. <br/><br/> Unité : Pourcentage <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 99,99 |

## <a name="metrics-dimensions"></a>Dimensions de mesures

Stockage Azure prend en charge les dimensions suivantes pour les mesures dans Azure Monitor.

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **BlobType** | Type d’objet blob pour les mesures d’objet Blob uniquement. Les valeurs prises en charge sont **BlockBlob**, **PageBlob**, et **Azure Data Lake Storage**. Append Blob est inclus dans BlockBlob. |
| **BlobTier** | Le Stockage Azure propose différents niveaux d’accès qui vous permettent de stocker vos objets blob de la manière la plus économique. Pour en savoir plus, consultez [Niveau du stockage Azure d’objets blob](../blobs/storage-blob-storage-tiers.md). Les valeurs prises en charge incluent : <br/> <li>**Hot** : Niveau de stockage chaud</li> <li>**Cool** : Niveau de stockage froid</li> <li>**Archivage** : Niveau de stockage archive</li> <li>**Premium** : Niveau Premium pour les objets blob de blocs</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60** : Types de niveau pour les objets blob de pages premium</li> <li>**Standard** : Type de niveau pour les objets blob de pages standard</li> <li>**Untiered** : Type de niveau pour un compte de stockage à usage général v1</li> |
| **GeoType** | Transaction du cluster principal ou secondaire. Les valeurs disponibles incluent **Principal** et **Secondaire**. S’applique au stockage Géo-redondant avec accès en lecture (RA-GRS) lors de la lecture d’objets à partir du locataire secondaire. |
| **ResponseType** | Type de réponse de transaction. Les valeurs disponibles incluent : <br/><br/> <li>**ServerOtherError**: toutes les autres erreurs côté serveur sauf celles décrites </li> <li>**ServerBusyError**: requête authentifiée qui a renvoyé un code d’état HTTP 503. </li> <li>**ServerTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Le délai d’expiration s’est produit en raison d’une erreur serveur. </li> <li>**AuthorizationError**: requête authentifiée qui a échoué en raison d’un accès aux données non autorisé ou d’un échec d’autorisation. </li> <li>**NetworkError**: requête authentifiée qui a échoué en raison d’erreurs réseau. Se produit généralement lorsqu’un client ferme une connexion avant la fin du délai d’expiration. </li>  <li>**ClientAccountBandwidthThrottlingError** : Une limitation de la bande passante est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError** : Une limitation du taux de requêtes est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Autre erreur de limitation côté client. ClientAccountBandwidthThrottlingError et ClientAccountRequestThrottlingError sont exclus.</li> <li>**ClientTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Si le délai d’expiration réseau du client ou le délai d’expiration de la requête est défini sur une valeur inférieure à ce qui est attendu par le service de stockage, il s’agit d’un délai d’expiration attendu. Sinon, il est signalé comme une erreur ServerTimeoutError.</li> </li> <li>**ClientOtherError**: toutes les autres erreurs côté client sauf celles décrites. </li> <li>**Réussite** : requête réussie</li> <li> **SuccessWithThrottling** : Demande réussie lorsqu’un client SMB est ralenti lors des premières tentatives, mais qu’il réussit lors des suivantes.</li> |
| **ApiName** | Nom de l’opération. Par exemple : <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Pour tous les noms d’opérations, voir [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Authentification** | Type d’authentification utilisé dans les transactions. Les valeurs disponibles incluent : <br/> <li>**AccountKey** : la transaction est authentifiée avec la clé du compte de stockage.</li> <li>**SAS** : la transaction est authentifiée avec des signatures d’accès partagé.</li> <li>**OAuth** : la transaction est authentifiée avec des jetons d’accès OAuth.</li> <li>**Anonymous** : la transaction est demandée anonymement. Elle n’inclut pas les demandes préalables.</li> <li>**AnonymousPreflight** : la transaction est une requête préalable.</li> |

Pour les mesures prenant en charge des dimensions, vous devez spécifier la valeur de la dimension pour afficher les valeurs de mesures correspondantes. Par exemple, si vous examinez la valeur **Transactions** pour des réponses réussies, vous devez filtrer la dimension **ResponseType** avec **Success**. Si vous examinez la valeur **BlobCount** pour BlockBlob, vous devez filtrer la dimension **BlobType** avec **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuité de service d’anciennes mesures

Les anciennes mesures sont disponibles en parallèle avec les mesures gérées d’Azure Monitor. La prise en charge permet de les conserver jusqu’à ce que Stockage Azure mette fin au service des anciennes mesures.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Les nouvelles métriques prennent-elles en charge le compte de stockage classique ?**

Non, les nouvelles métriques d’Azure Monitor ne prennent en charge que les comptes de stockage Azure Resource Manager. Si vous souhaitez utiliser des métriques sur les comptes de stockage, vous devez migrer vers le compte de stockage Azure Resource Manager. Voir [Migrer vers Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Le stockage Azure prend-il en charge les métriques de disques managés ou de disques non managés ?**

Non, le Calcul Azure prend en charge les métriques sur les disques. Consultez l’[article](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) pour plus de détails.

**Comment mapper et migrer des métriques classiques avec de nouvelles métriques ?**

Vous pouvez trouver le mappage détaillé entre les métriques classiques et de nouvelles métriques dans [Migration des métriques de Stockage Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Étapes suivantes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
