---
title: Surveiller le Stockage Azure | Microsoft Docs
description: Découvrez comment surveiller les performances et la disponibilité du stockage Azure. Surveillez les données Stockage Azure, obtenez plus de détails sur la configuration, et analysez les métriques et les données de journal.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2cc275028b64c3df06e2c0275bc0f4ac21fa0f6e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020148"
---
# <a name="monitor-azure-storage"></a>Surveiller le Stockage Azure

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par le stockage Azure et comment vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes.

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Cette préversion active les journaux des objets blob (qui incluent Azure Data Lake Storage Gen2), les fichiers, les files d’attente et les tables. Cette fonctionnalité est disponible pour tous les comptes de stockage créés avec le modèle de déploiement Resource Manager. Voir [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Présentation de Monitor

La page **Vue d’ensemble** du Portail Azure pour chaque ressource de stockage comprend un bref aperçu de l’utilisation de la ressource, comme les demandes et de la facturation horaire. Ces informations sont utiles, mais elles ne constituent qu’une petite quantité des données de supervision disponibles. Certaines de ces données sont collectées automatiquement et peuvent être analysées dès que vous créez la ressource de stockage. Vous pouvez activer d’autres types de collecte de données avec une certaine configuration.

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?
Le stockage Azure crée des données de supervision à l’aide d’[Azure Monitor](../../azure-monitor/overview.md), qui est un service de supervision de pile complète dans Azure. Azure Monitor fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure, ainsi que les ressources locales et celles présentes dans d’autres clouds. 

Pour en savoir plus sur Azure Monitor, consultez [Supervision de ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md). L’article décrit les sujets suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées à partir du Stockage Azure. Les exemples montrent comment configurer la collecte de données et analyser ces données avec les outils Azure.

## <a name="monitor-data-from-azure-storage"></a>Surveiller des données du Stockage Azure

Le stockage Azure collecte les mêmes types de données de supervision que les autres ressources Azure, qui sont décrites dans [Superviser des données à partir de ressources Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Pour obtenir davantage d’informations sur les journaux et les métriques créés par le Stockage Azure, consultez [Informations de référence sur les données de supervision du stockage Azure](monitor-storage-reference.md).

Les métriques et les journaux d’Azure Monitor ne prennent en charge que les comptes de stockage Azure Resource Manager. Azure Monitor ne prend pas en charge les comptes de stockage classiques. Si vous souhaitez utiliser des métriques ou des journaux sur un compte de stockage classique, vous devez migrer vers un compte de stockage Azure Resource Manager. Voir [Migrer vers Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Vous pouvez continuer à utiliser les métriques et les journaux classiques si vous le souhaitez. En fait, les métriques et les journaux classiques sont disponibles parallèlement aux métriques et journaux dans Azure Monitor. La prise en charge reste en place jusqu’à ce que le stockage Azure mette fin au service sur les métriques et les journaux hérités.

### <a name="logs-in-azure-monitor-preview"></a>Journaux dans Azure Monitor (préversion)

Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison de blob, mais pas dans ses points de terminaison de table ou de file d’attente, seuls les journaux d’activité qui appartiennent au service Blob sont créés. Les journaux de stockage Azure contiennent des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort.

#### <a name="log-authenticated-requests"></a>Journaliser des requêtes authentifiées

 Les types de demandes authentifiées suivants sont enregistrés :

- Demandes ayant réussi
- Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs
- Demandes utilisant une signature d’accès partagé (SAS) ou OAuth, y compris les demandes ayant réussi et ayant échoué
- Demandes de données d’analyse (données de journal classique dans le conteneur **$logs** et données de métriques de classe dans les tables **$metric**)

Les demandes effectuées par le service de stockage lui-même, telles que la création ou la suppression d’un journal, ne sont pas consignées. Pour obtenir une liste complète des données enregistrées, consultez [Opérations et messages d’état enregistrés de stockage](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de stockage](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Journaliser des requêtes anonymes

 Les types de demandes anonymes suivants sont enregistrés :

- Demandes ayant réussi
- Erreurs de serveur
- Erreurs d’expiration pour le client et le serveur
- Demandes GET ayant échoué avec le code d’erreur 304 (non modifié)

Aucune autre demande anonyme ayant échoué n'est enregistrée. Pour obtenir une liste complète des données enregistrées, consultez [Opérations et messages d’état enregistrés de stockage](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de stockage](monitor-storage-reference.md).

## <a name="configuration"></a>Configuration

Les métriques de la plateforme et le journal d’activité sont collectés automatiquement, mais vous devez créer un paramètre de diagnostic pour collecter les journaux des ressources ou les transférer en dehors d’Azure Monitor. Pour connaître le processus de création d’un paramètre de diagnostic à l’aide du Portail Azure, de l’interface Azure CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../../azure-monitor/platform/diagnostic-settings.md).

Lorsque vous créez un paramètre de diagnostic, choisissez le type de stockage pour lequel vous souhaitez activer les journaux (tel que : objet blob, file d’attente, table ou fichier). Data Lake Storage Gen2 n’apparaît pas en tant que type de stockage. Cela est dû au fait que Data Lake Storage Gen2 est un ensemble de fonctionnalités disponibles pour le stockage Blob. 

Si vous créez le paramètre de diagnostic dans le Portail Azure, vous pouvez sélectionner la ressource dans une liste. Si vous utilisez PowerShell ou Azure CLI, vous devez utiliser l’ID de ressource du type de stockage. Vous pouvez trouver l’ID de ressource dans le portail Azure en ouvrant la page **Propriétés** de votre compte de stockage.

Vous devez également spécifier les catégories d’opérations pour lesquelles vous souhaitez collecter les journaux. Les catégories pour le Stockage Azure sont répertoriées dans ce tableau.

| Category | Description |
|:---|:---|
| StorageRead | Opérations de lecture sur des objets. |
| StorageWrite | Opérations d’écriture sur des objets. |
| StorageDelete | Opérations de suppression sur des objets. |

## <a name="analyze-metric-data"></a>Analyser les données de métriques

Vous pouvez analyser les métriques de Stockage Azure avec des métriques issues d’autres services Azure à l’aide de Metrics Explorer. Ouvrez Metrics Explorer en choisissant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Cet exemple montre comment afficher les **Transactions** au niveau du compte.

![Capture d’écran d’accès aux mesures dans le Portail Azure](./media/monitor-storage/access-metrics-portal.png)

Pour les métriques prenant en charge des dimensions, vous pouvez les filtrer avec la valeur de dimension souhaitée. Cet exemple montre comment afficher les **Transactions** au niveau du compte sur une opération spécifique en sélectionnant des valeurs pour la dimension **Nom de l’API**.

![Capture d’écran d’accès aux mesures avec une dimension dans le Portail Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Pour obtenir la liste complète des dimensions prises en charge par le stockage Azure, consultez [Dimensions des métriques](monitor-storage-reference.md#metrics-dimensions).

Toutes les métriques pour le Stockage Azure se trouvent dans ces espaces de noms :

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Pour obtenir la liste de toutes les métriques de prise en charge d’Azure Monitor, comprenant le stockage Azure, consultez [Métriques prises en charge avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Accéder aux mesures

> [!TIP]
> Pour afficher des exemples Azure CLI ou .NET, choisissez l’onglet correspondant listé ici.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Dresser la liste de la définition des métriques

Vous pouvez dresser la liste de la définition des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez l’applet de commande [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0).

Dans cet exemple, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources sur les pages **Propriétés** de votre compte de stockage sur le Portail Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Lire les valeurs des métriques

Vous pouvez lire les valeurs des métriques de niveau compte de votre compte de stockage ou le service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez l’applet de commande [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Dresser la liste de la définition des métriques de niveau compte

Vous pouvez dresser la liste de la définition des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez la commande [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions).
 
Dans cet exemple, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources sur les pages **Propriétés** de votre compte de stockage sur le Portail Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lire les valeurs des métriques de niveau compte

Vous pouvez lire les valeurs des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez la commande [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor fournit des [kits de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pour lire des définitions et valeurs de mesures. L’[exemple de code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) montre comment utiliser le kit de développement logiciel (SDK) avec des paramètres différents. Vous devez utiliser `0.18.0-preview` ou une version ultérieure pour les mesures de stockage.
 
Dans ces exemples, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources sur les pages **Propriétés** de votre compte de stockage sur le Portail Azure.

Remplacez la variable `<subscription-ID>` par l’ID de votre abonnement. Pour obtenir des conseils sur la façon d’obtenir des valeurs pour `<tenant-ID>`, `<application-ID>` et `<AccessKey>`, consultez [Utiliser le portail pour créer une application et un principal du service Azure AD pouvant accéder aux ressources](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Dresser la liste de la définition des métriques de niveau compte

L’exemple suivant montre comment répertorier une définition de mesures au niveau du compte :

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="read-account-level-metric-values"></a>Lire les valeurs des métriques de niveau compte

L’exemple suivant montre comment lire des données `UsedCapacity` au niveau du compte :

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="read-multidimensional-metric-values"></a>Lire les valeurs des métriques multidimensionnelles

Pour les mesures multidimensionnelles, vous devez définir le filtre des métadonnées si vous souhaitez lire les données métriques sur des valeurs de dimension spécifiques.

L’exemple suivant montre comment lire les données de mesures sur la métrique prenant en charge plusieurs dimensions :

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyze-log-data"></a>Analyser les données de journal

Vous pouvez accéder aux journaux des ressources en tant qu’objet blob dans un compte de stockage, en tant que données d’événement ou par le biais de requêtes Log Analytics.

Pour obtenir une référence détaillée des champs qui apparaissent dans ces journaux, consultez [Informations de référence sur les données de supervision du Stockage Azure](monitor-storage-reference.md).

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Cette préversion active les journaux pour les objets blob (qui comprennent Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

### <a name="access-logs-in-a-storage-account"></a>Accéder aux journaux dans un compte de stockage

Les journaux apparaissent sous forme d’objets blob stockés dans un conteneur du compte de stockage cible. Les données sont collectées et stockées dans un objet blob individuel sous la forme d’une charge utile JSON délimitée par une ligne. Le nom d’objet blob suit cette convention d’affectation de noms :

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Voici un exemple :

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Accéder aux journaux dans un Event Hub

Les journaux envoyés à un hub d’événements ne sont pas stockés en tant que fichier, mais vous pouvez vérifier que le hub d’événements a reçu les informations du journal. Dans le Portail Azure, accédez à votre Event Hub et vérifiez que le nombre de **messages entrants** est supérieur à zéro. 

![Journaux d’audit](media/monitor-storage/event-hub-log.png)

Vous pouvez accéder aux données de journal envoyées à votre hub d’événements et les lire à l’aide d’outils d’analyse et de gestion d’événements et d’informations de sécurité. Pour plus d’informations, consultez [Que faire avec les données de supervision envoyées à mon hub d’événements ?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-a-log-analytics-workspace"></a>Accéder aux journaux dans un espace de travail Log Analytics

Vous pouvez accéder aux journaux envoyés à un espace de travail Log Analytics en utilisant des requêtes de journal Azure Monitor.

Pour plus d’informations, voir [Bien démarrer avec Log Analytics dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Les données sont stockées dans ces tableaux.

| Table de charge de travail | Description |
|:---|:---|
|StorageBlobLogs | Journaux qui décrivent l’activité dans le stockage d’objets blob |
|StorageFileLogs | Journaux qui décrivent l’activité dans les partages de fichiers |
|StorageQueueLogs | Journaux qui décrivent l’activité dans les files d’attente|
|StorageTableLogs| Journaux qui décrivent l’activité dans les tables|

Les journaux de Data Lake Storage Gen2 n’apparaissent pas dans une table dédiée. Cela est dû au fait que Data Lake Storage Gen2 n’est pas un service. Il s’agit d’un ensemble de fonctionnalités que vous pouvez activer sur un compte de stockage Blob. Si vous avez activé ces fonctionnalités, les journaux continueront d’apparaître dans la table StorageBlobLogs. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Requêtes Log Analytics de stockage Azure dans Azure Monitor

Voici quelques requêtes que vous pouvez entrer dans le volet **Recherche dans les journaux**. Elles vous aideront à superviser vos comptes de stockage Azure. Ces requêtes fonctionnent avec le [nouveau langage](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Utilisez ces requêtes pour mieux superviser vos comptes de Stockage Azure :

* Pour lister les 10 erreurs les plus courantes au cours des trois derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Pour lister les 10 principales opérations qui ont provoqué le plus d’erreurs au cours des trois derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Pour lister les 10 opérations ayant la latence de bout en bout la plus longue au cours des trois derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Pour lister toutes les opérations ayant provoqué des erreurs de limitation côté serveur au cours des trois derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Pour lister toutes les demandes avec un accès anonyme au cours des trois derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Pour créer un graphique à secteurs des opérations utilisées au cours des trois derniers jours.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Questions fréquentes (FAQ)

**Le stockage Azure prend-il en charge les métriques de disques managés ou de disques non managés ?**

Non. Azure Compute prend en charge les métriques sur les disques. Pour obtenir davantage d’informations, consultez [Métriques par disque pour les disques managés et non managés](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques créés par le Stockage Azure, consultez [Informations de référence sur les données de supervision du Stockage Azure](monitor-storage-reference.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Superviser des ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Pour plus d’informations sur la migration des métriques, consultez [Migration des métriques de Stockage Azure](./storage-metrics-migration.md).
