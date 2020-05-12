---
title: Supervision du stockage Azure | Microsoft Docs
description: Découvrez comment surveiller les performances et la disponibilité du stockage Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82710220"
---
# <a name="monitoring-azure-storage"></a>Supervision du stockage Azure

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par le stockage Azure et comment vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes.

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Cette préversion active les journaux pour les objets blob (y compris Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

## <a name="monitor-overview"></a>Présentation de Monitor

La page **Vue d’ensemble** du portail Azure pour chaque ressource de stockage comprend un bref aperçu de l’utilisation de la ressource, y compris de sa demande et de la facturation horaire. Il s’agit d’informations utiles, mais qui ne constituent qu’une petite quantité des données de supervision disponibles. Certaines de ces données sont collectées automatiquement et peuvent être analysées dès que vous créez la ressource de stockage. Vous pouvez activer d’autres types de collecte de données avec une certaine configuration.

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?
Le stockage Azure crée des données de supervision avec [Azure Monitor](../../azure-monitor/overview.md), un service de supervision de pile complète dans Azure qui fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et sur site. 

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) qui décrit les éléments suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées à partir du stockage Azure et en fournissant des exemples de configuration de collecte de données et d’analyse de ces données avec les outils Azure.

## <a name="monitoring-data-from-azure-storage"></a>Données de supervision issues du stockage Azure

Le stockage Azure collecte les mêmes types de données de supervision que les autres ressources Azure, qui sont décrites dans [Supervision des données à partir de ressources Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Consultez [Informations de référence sur les données de supervision du stockage Azure](monitor-storage-reference.md) pour obtenir une référence détaillée des journaux et des métriques créés par le stockage Azure.

Les métriques et les journaux d’Azure Monitor ne prennent en charge que les comptes de stockage Azure Resource Manager. Azure Monitor ne prend pas en charge les comptes de stockage classiques. Si vous souhaitez utiliser des métriques ou des journaux sur un compte de stockage classique, vous devez migrer vers le compte de stockage Azure Resource Manager. Voir [Migrer vers Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Vous pouvez continuer à utiliser les métriques et les journaux classiques si vous le souhaitez. En fait, les métriques et les journaux classiques sont disponibles parallèlement aux métriques et journaux dans Azure Monitor. La prise en charge reste en place jusqu’à ce que le stockage Azure mette fin au service sur les métriques et les journaux hérités. 

### <a name="logs-in-azure-monitor-preview"></a>Journaux dans Azure Monitor (préversion)

Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison d’objet blob, mais pas dans ses points de terminaison de table ou de file d’attente, seuls les journaux relatifs au service blob seront créés. Les journaux de stockage Azure contiennent des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort.

#### <a name="logging-authenticated-requests"></a>Enregistrement des demandes authentifiées

 Les types de demandes authentifiées suivants sont enregistrés :

- Demandes ayant réussi
- Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs
- Requêtes utilisant une signature d’accès partagé (SAS) ou OAuth, y compris les requêtes ayant réussi et ayant échoué
- Demandes de données d’analyse (données de journal classique dans le conteneur **$logs** et données de métriques de classe dans les tables **$metric**)

Les demandes effectuées par le service de stockage lui-même, telles que la création ou la suppression d’un journal, ne sont pas consignées. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés de stockage](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de stockage](monitor-storage-reference.md).

#### <a name="logging-anonymous-requests"></a>Journalisation des demandes anonymes

 Les types de demandes anonymes suivants sont enregistrés :

- Demandes ayant réussi
- Erreurs de serveur
- Erreurs de délai d’expiration pour le client et le serveur
- Demandes GET ayant échoué avec le code d’erreur 304 (non modifié)

Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés de stockage](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de stockage](monitor-storage-reference.md).

## <a name="configuration"></a>Configuration

Les métriques de la plateforme et le journal d’activité sont collectés automatiquement, mais vous devez créer un paramètre de diagnostic pour collecter les journaux des ressources ou les transférer en dehors d’Azure Monitor. Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../../azure-monitor/platform/diagnostic-settings.md).

Lorsque vous créez un paramètre de diagnostic, vous devez choisir le type de stockage pour lequel vous souhaitez activer les journaux (objet blob, file d’attente, table, fichier). Si vous créez le paramètre de diagnostic dans le portail Azure, vous pouvez sélectionner la ressource dans une liste. Si vous utilisez PowerShell ou Azure CLI, vous devez utiliser l’ID de ressource du type de stockage. Vous pouvez trouver l’ID de ressource dans le portail Azure en ouvrant la page **Propriétés** de votre compte de stockage.

Vous devez également spécifier les catégories d’opérations pour lesquelles collecter les journaux. Les catégories pour le stockage Azure sont répertoriées dans le tableau suivant :

| Category | Description |
|:---|:---|
| StorageRead | Opérations de lecture sur les objets blob  |
| StorageWrite | Opérations d’écriture sur les objets blob |
| StorageDelete | Opérations de suppression sur les objets blob |

## <a name="analyzing-metric-data"></a>Analyse des données de métrique

Vous pouvez analyser les métriques de stockage Azure avec des métriques issues d’autres services Azure à l’aide de Metrics Explorer. Ouvrez Metrics Explorer en choisissant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

L’exemple suivant montre comment afficher les **Transactions** au niveau du compte.

![capture d’écran d’accès aux mesures dans le portail Azure](./media/monitor-storage/access-metrics-portal.png)

Pour les métriques prenant en charge des dimensions, vous pouvez les filtrer avec la valeur de dimension souhaitée. L’exemple suivant montre comment afficher **Transactions** au niveau du compte sur une opération spécifique en sélectionnant des valeurs pour la dimension **Nom de l’API**.

![capture d’écran d’accès aux mesures avec une dimension dans le portail Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Pour obtenir la liste complète des dimensions prises en charge par le stockage Azure, consultez [Dimensions des métriques](monitor-storage-reference.md#metrics-dimensions).

Toutes les métriques pour le stockage Azure se trouvent dans les espaces de noms suivants :

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Pour obtenir la liste de toutes les métriques de prise en charge d’Azure Monitor (y compris le stockage Azure), consultez [Métriques prises en charge avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Accéder aux mesures

> [!TIP]
> Pour afficher des exemples Azure CLI ou .NET, choisissez l’onglet correspondant ci-dessous.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Dresser la liste de la définition des métriques

Vous pouvez dresser la liste de la définition des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez l’applet de commande [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0).

Dans cet exemple, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources dans les pages **Propriétés** de votre compte de stockage dans le portail Azure.

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

#### <a name="list-account-level-metric-definition"></a>Dresser la liste de la définition des métriques de niveau compte

Vous pouvez dresser la liste de la définition des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez la commande [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions).
 
Dans cet exemple, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources dans les pages **Propriétés** de votre compte de stockage dans le portail Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lire les valeurs des métriques de niveau compte

Vous pouvez lire les valeurs des métriques de votre compte de stockage ou du service de stockage individuel, tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Utilisez la commande [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor fournit des [kits de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pour lire des définitions et valeurs de mesures. L’[exemple de code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) montre comment utiliser le kit de développement logiciel (SDK) avec des paramètres différents. Vous devez utiliser `0.18.0-preview` ou version ultérieure pour les mesures de stockage.
 
Dans ces exemples, remplacez l’espace réservé `<resource-ID>` par l’ID de ressource du compte de stockage complet ou par l’ID de ressource d’un service de stockage individuel tel que le service d’objets blob, de fichiers, de tables ou de files d’attente. Vous pouvez trouver les ID de ces ressources dans les pages **Propriétés** de votre compte de stockage dans le portail Azure.

Remplacez la variable `<subscription-ID>` par l’ID de votre abonnement.  Pour obtenir des conseils sur la façon d’obtenir des valeurs pour `<tenant-ID>`, `<application-ID>` et `<AccessKey>`, consultez [Procédure : Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Dresser la liste de la définition des métriques de niveau compte

Les exemples suivants montrent comment dresser la liste de la définition des métriques au niveau du compte :

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

L’exemple suivant montre comment lire des données `UsedCapacity` au niveau du compte :

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

#### <a name="read-multi-dimensional-metric-values"></a>Lire les valeurs des métriques multidimensionnelles

Pour les mesures multidimensionnelles, vous devez définir le filtre des métadonnées si vous souhaitez lire les données métriques sur la valeur de dimension spécifique.

L’exemple suivant montre comment lire les données de mesures sur la métrique prenant en charge plusieurs dimensions :

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

## <a name="analyzing-log-data"></a>Analyse des données de journal

Vous pouvez accéder aux journaux des ressources en tant qu’objet blob dans un compte de stockage, en tant que données d’événement ou par le biais de requêtes Log Analytics.

Consultez [Informations de référence sur les données de supervision du stockage Azure](monitor-storage-reference.md) pour obtenir une référence détaillée des champs qui apparaissent dans ces journaux.

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://www.microsoft.com).  Cette préversion active les journaux pour les objets blob (y compris Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

### <a name="access-logs-in-a-storage-account"></a>Accéder aux journaux dans un compte de stockage

Les journaux apparaissent sous forme d’objets blob stockés dans un conteneur du compte de stockage cible. Les données sont collectées et stockées dans un objet blob individuel sous la forme d’une charge utile JSON délimitée par une ligne. Ce nom d’objet blob suit la convention d’affectation de noms suivante :

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Voici un exemple :

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Accéder aux journaux dans un hub d’événements

Les journaux envoyés à un hub d’événements ne sont pas stockés en tant que fichier, mais vous pouvez vérifier que le hub d’événements a reçu les informations du journal. Dans le portail Azure, accédez à votre hub d’événements, puis vérifiez que le nombre de **messages entrants** est supérieur à zéro. 

![Journaux d’audit](media/monitor-storage/event-hub-log.png)

Vous pouvez accéder aux données de journal envoyées à votre hub d’événements et les lire à l’aide d’outils d’analyse et de gestion d’événements et d’informations de sécurité. Pour plus d’informations, consultez [Que faire avec les données de supervision envoyées à mon hub d’événements ?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Accéder aux journaux dans l’espace de travail Log Analytics

Vous pouvez accéder aux journaux envoyés à un espace de travail Log Analytics en utilisant des requêtes de journal Azure Monitor.

Consultez [Bien démarrer avec Log Analytics dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Les données sont stockées dans les tables suivantes.

| Table de charge de travail | Description |
|:---|:---|
|StorageBlobLogs | Journaux qui décrivent l’activité dans le stockage d’objets blob |
|StorageFileLogs | Journaux qui décrivent l’activité dans les partages de fichiers |
|StorageQueueLogs | Journaux qui décrivent l’activité dans les files d’attente|
|StorageTableLogs| Journaux qui décrivent l’activité dans les tables|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Requêtes Log Analytics de stockage Azure dans Azure Monitor

Voici quelques requêtes que vous pouvez entrer dans le volet **Recherche dans les journaux**. Elles vous aideront à superviser vos comptes de stockage Azure. Ces requêtes fonctionnent avec le [nouveau langage](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Voici des requêtes que vous pouvez utiliser pour mieux superviser vos comptes de stockage Azure.

* Pour lister les 10 erreurs les plus courantes au cours des 3 derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Pour lister les 10 opérations ayant causé le plus d’erreurs au cours des 3 derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Pour lister les 10 opérations ayant la latence de bout en bout la plus longue au cours des 3 derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Pour lister toutes les opérations ayant provoqué des erreurs de limitation côté serveur au cours des 3 derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Pour lister toutes les demandes avec un accès anonyme au cours des 3 derniers jours.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Pour créer un graphique à secteurs des opérations utilisées au cours des 3 derniers jours.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Questions fréquentes (FAQ)

**Le stockage Azure prend-il en charge les métriques de disques managés ou de disques non managés ?**

Non, le Calcul Azure prend en charge les métriques sur les disques. Consultez l’[article](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) pour plus de détails.

## <a name="next-steps"></a>Étapes suivantes

- [Informations de référence sur les données de supervision du stockage Azure](monitor-storage-reference.md) : obtenez une référence des journaux et des métriques créés par le stockage Azure.
- [Supervision de ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) : pour plus d’informations sur la supervision des ressources Azure.
- [Migration des métriques de stockage Azure](./storage-metrics-migration.md)

