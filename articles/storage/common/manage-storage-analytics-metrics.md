---
title: Activer et gérer les métriques Azure Storage Analytics (classique) | Microsoft Docs
description: Découvrez comment activer, modifier et afficher les métriques Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221426"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Activer et gérer les métriques Azure Storage Analytics (classique)

[Azure Storage Analytics](storage-analytics.md) fournit des métriques pour tous les services de stockage pour les objets Blob, les files d’attente et les tables. Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour configurer les métriques enregistrées pour votre compte et des graphiques qui fournissent des représentations visuelles de vos données de métriques. Cet article explique comment activer et gérer les métriques. Pour savoir comment activer les journaux, consultez [Activer et gérer les journaux Azure Storage Analytics (classique)](manage-storage-analytics-logs.md).

Nous vous recommandons de consulter [Azure Monitor pour le stockage](../../azure-monitor/insights/storage-insights-overview.md) (préversion). Il s’agit d’une fonctionnalité d’Azure Monitor qui fournit une analyse complète de vos comptes de Stockage Azure en présentant une vue unifiée des performances, de la capacité et de la disponibilité de vos services de Stockage Azure. Vous n’avez pas besoin d’activer ou de configurer quoi que ce soit, et vous pouvez afficher immédiatement ces métriques à partir des graphiques interactifs prédéfinis et d’autres visualisations incluses.

> [!NOTE]
> L’analyse des données de surveillance dans le portail Azure occasionne des frais. Pour plus d’informations, consultez [Storage Analytics](storage-analytics.md).
>
> Les comptes de stockage blob de blocs de niveau de performance Premium ne prennent pas en charge les métriques Storage Analytics. Si vous souhaitez afficher les métriques pour des comptes de stockage blob de blocs de niveau de performance Premium, envisagez d’utiliser [Métriques de Stockage Azure dans Azure Monitor](../blobs/monitor-blob-storage.md).
>
> Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Activer les mesures

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, puis le nom du compte de stockage pour ouvrir le tableau de bord du compte.

2. Sélectionnez **Paramètres de diagnostic (classique)** dans la section **Supervision (classique)** du panneau du menu.
   
   ![Capture d'écran mettant en évidence l'option Paramètres de diagnostic (classique) dans la section Surveillance (classique).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Sélectionnez le **type** de données de métrique pour chaque **service** à surveiller et la **stratégie de rétention** pour les données. Vous pouvez également désactiver la surveillance en définissant **l’état** sur **Off**.

   > [!div class="mx-imgBorder"]
   > ![Configurez la journalisation dans le portail Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Pour définir la stratégie de rétention de données, déplacez le curseur **Rétention (en jours)** ou entrez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). La valeur par défaut pour les nouveaux comptes de stockage est de sept jours. Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance.

   > [!WARNING]
   > Les métriques sont stockées sous forme de données dans votre compte. Les données des métriques peuvent s’accumuler dans votre compte au fil du temps, ce qui peut augmenter le coût du stockage. Si vous n’avez besoin des données des métriques que pendant une durée limitée, vous pouvez réduire vos coûts en modifiant la stratégie de conservation des données. Les données des métriques obsolètes (antérieures à votre stratégie de rétention des données) sont supprimées par le système. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données des métriques pour votre compte. Pour plus d’informations, consultez la page [Facturation sur les métriques de stockage](storage-analytics-metrics.md#billing-on-storage-metrics).
   >

4. Une fois la configuration de la surveillance terminée, sélectionnez **Enregistrer**.

Un ensemble de métriques par défaut est affiché dans des graphiques dans le panneau **Vue d’ensemble**, ainsi que dans le panneau **Métriques (classique)** . Une fois que vous avez activé les métriques d’un service, l’affichage des données dans les graphiques peut prendre jusqu’à une heure. Vous pouvez sélectionner **Modifier** sur n’importe quel graphique de métrique pour configurer les métriques qui sont affichées dans le graphique.

Vous pouvez désactiver la collecte de métriques et la journalisation en définissant **l’état** sur **Off**.

> [!NOTE]
> Le stockage Azure utilise un [stockage Table](storage-introduction.md#table-storage) pour stocker les métriques pour votre compte de stockage, et stocke les métriques dans des tables de votre compte. Pour plus d'informations, consultez [Stockage des métriques](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ouvrez une fenêtre Commande Windows PowerShell.

2. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

3. Si votre identité est associée à plusieurs abonnements, définissez votre abonnement actif.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

5. Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage. 

6. Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer les métriques de stockage dans votre compte de stockage. Utilisez la cmdlet Azure PowerShell **Set-AzStorageServiceMetricsProperty** pour changer les paramètres actuels. 

   La commande suivante active les métriques par minute pour le service Blob dans votre compte de stockage avec une période de rétention de cinq jours.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Cette applet de commande utilise les paramètres suivants :  

   - **ServiceType** : les valeurs possibles sont **Blob**, **File d’attente**, **Table** et **Fichier**.
   - **MetricsType** : peut avoir la valeur **Heure** ou **Minute**.  
   - **MetricsLevel** : Les valeurs possibles sont les suivantes :
      - **Aucun** : Désactive la surveillance.
      - **Service** : Collecte des mesures telles que l’entrée et la sortie, la disponibilité, la latence et les pourcentages de réussite, qui sont agrégées pour les services BLOB, File d’attente, Table et Fichier.
      - **ServiceAndApi** : Outre les mesures de service, collecte le même ensemble de mesures pour chaque opération de stockage de l’API du service Stockage Azure.

   La commande suivante récupère le niveau actuel des métriques par heure et la période de rétention en jours pour le service BLOB dans votre compte de stockage par défaut :  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Pour plus d’informations sur la configuration des cmdlets Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, voir [Installation et configuration d’Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Pour plus d’informations sur l’utilisation d’un langage .NET afin de configurer les mesures de stockage, consultez les [bibliothèques clientes de Stockage Azure pour .NET](/dotnet/api/overview/azure/storage).  

Pour obtenir des informations générales sur la configuration des mesures de stockage avec l’API REST, voir [Activation et configuration de Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Pour plus d’informations sur l’utilisation d’un langage .NET afin de configurer les mesures de stockage, consultez les [bibliothèques clientes de Stockage Azure pour .NET](/dotnet/api/overview/azure/storage).  

Pour obtenir des informations générales sur la configuration des mesures de stockage avec l’API REST, voir [Activation et configuration de Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Afficher les métriques dans un graphique

Après que vous avez configuré les métriques d’analyse du stockage pour surveiller votre compte de stockage, l’analyse du stockage enregistre les métriques dans des tables connues dans votre compte de stockage. Vous pouvez configurer des graphiques permettant de consulter des métriques horaires dans le [portail Azure](https://portal.azure.com).

La procédure suivante permet de choisir les métriques de stockage à afficher dans un graphique de métrique.

1. Commencez par afficher un graphique de métrique de stockage dans le portail Azure. Vous trouverez des graphiques dans le **panneau du compte de stockage** et dans le panneau **Métriques (classique)** .

   Dans cet exemple, nous utilisons le graphique suivant qui apparaît dans le **panneau du compte de stockage** :

   ![Sélection de graphique dans le portail Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Cliquez n’importe où dans le graphique pour le modifier.

3. Ensuite, sélectionnez la **Plage de temps** des métriques à afficher dans le graphique et le **service** (Blob, File d’attente, Table, Fichier) dont vous voulez afficher les métriques. Ici, nous avons choisi d’afficher les métriques de la semaine précédente pour le service Blob :

   ![Sélection de la période et du service dans le panneau Modifier le graphique](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Sélectionnez la **métrique** individuelle à afficher dans le graphique, puis cliquez sur **OK**.

   ![Sélection de métrique individuelle dans le panneau Modifier le graphique](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Les paramètres de votre graphique n’ont aucune incidence sur la collecte, l’agrégation ou le stockage des données de surveillance dans le compte de stockage.

#### <a name="metrics-availability-in-charts"></a>Disponibilité des métriques dans les graphiques

La liste des métriques disponibles change selon le service que vous avez choisi dans la liste déroulante et le type d’unité du graphique que vous modifiez. Par exemple, vous pouvez sélectionner les métriques de pourcentage comme *PercentNetworkError* et *PercentThrottlingError* uniquement si vous modifiez un graphique qui affiche les unités en pourcentage :

![Graphique de pourcentage d’erreur de demande dans le portail Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Résolution des métriques

Les métriques que vous avez sélectionnées dans **Diagnostics** déterminent la résolution des métriques qui sont disponibles pour votre compte :

* La surveillance **Agréger** fournit des métriques telles que l’entrée/la sortie, la disponibilité, la latence et les pourcentages de réussite. Ces métriques sont agrégées à partir des services Blob, File d’attente, Table et Fichier.
* **Par API** offre une résolution plus pointue, avec les métriques disponibles pour les opérations de stockage individuelles, en plus des agrégats au niveau du service.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Télécharger des métriques pour les archiver ou les analyser localement

Si vous souhaitez télécharger les métriques pour un stockage à long terme ou pour les analyser localement, vous devez utiliser un outil ou écrire du code pour lire les tables. Les tables ne sont pas visibles si vous répertoriez toutes les tables dans votre compte de stockage, mais vous pouvez y accéder directement par nom. De nombreux outils de consultation du stockage prennent en charge ces tables et vous permettent de les afficher directement. Pour obtenir la liste des outils disponibles, consultez la rubrique relative aux [outils clients du Stockage Azure](./storage-explorers.md).

|Mesures|Noms de tables|Notes| 
|-|-|-|  
|Métriques toutes les heures|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Dans les versions antérieures au 15 août 2013, ces tables étaient connues sous les noms suivants :<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Les mesures pour le service Fichier sont disponibles depuis la version du 5 avril 2015.|  
|Métriques par minute|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Ne peut être activé qu’avec PowerShell ou de manière programmatique.<br /><br /> Les mesures pour le service Fichier sont disponibles depuis la version du 5 avril 2015.|  
|Capacité|$MetricsCapacityBlob|Service Blob uniquement.|  

Vous trouverez des informations complètes sur les schémas de ces tables dans la section [Schéma de table de mesures Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). Les exemples de lignes ci-dessous montrent uniquement un sous-ensemble des colonnes disponibles, mais illustrent les différentes façons dont les mesures de stockage enregistrent ces informations :  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Disponibilité|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Dans cet exemple de données de mesures par minute, la clé de partition utilise une résolution d’une minute. La clé de ligne identifie le type d’informations stockées dans la ligne. Les informations incluent le type d’accès et de requête :  

-   Le type d’accès a la valeur **user** ou **system**, **user** correspondant à toutes les requêtes de l’utilisateur au service de stockage et **system**, à toutes les demandes formulées par Storage Analytics.  
-   Le type de requête peut avoir la valeur **all**, auquel cas il s’agit d’une ligne de résumé, ou identifie l’API spécifique comme **QueryEntity** ou **UpdateEntity**.  

Les exemples de données ci-dessus montrent tous les enregistrements pour une seule minute (à partir de 11h00). Ainsi, la somme des requêtes **QueryEntities**, plus le nombre de requêtes **QueryEntity** et **UpdateEntity** donne un résultat de sept, ce qui correspond au total indiqué sur la ligne **user:All**. De même, vous pouvez déduire la latence de bout en bout moyenne (104,4286) sur la ligne **user:All** en effectuant le calcul suivant : ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Afficher les données des métriques programmatiquement

L’exemple de code suivant en C# accède aux mesures par minute pour une plage de minutes et affiche les résultats dans une fenêtre de console. L’exemple de code utilise la version 4.x ou une version ultérieure de la bibliothèque cliente de stockage Azure. Celle-ci comprend la classe **CloudAnalyticsClient**, qui simplifie l’accès aux tables de mesures de stockage. 

> [!NOTE]
> La classe **CloudAnalyticsClient** n’est pas incluse dans la bibliothèque de client Stockage Blob Azure v12 pour .NET. Au **31 août 2023**, les métriques Storage Analytics, également appelées *métriques classiques* seront retirées. Pour plus d’informations, consultez l’[annonce officielle](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Si vous utilisez des métriques classiques, nous vous recommandons de passer aux métriques dans Azure Monitor avant cette date. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Ajouter des graphiques de métriques au tableau de bord du portail

Vous pouvez ajouter des graphiques de métriques Stockage Azure pour l’un de vos comptes de stockage au tableau de bord du portail.

1. Cliquez sur **Modifier le tableau de bord** lorsque le tableau de bord est affiché dans le [portail Azure](https://portal.azure.com).
1. Dans la **Galerie de vignettes**, sélectionnez **Recherche des vignettes par** > **Type**.
1. Sélectionnez **Type** > **Comptes de stockage**.
1. Dans **Ressources**, sélectionnez le compte de stockage dont vous souhaitez ajouter les métriques au tableau de bord.
1. Sélectionnez **Catégories** > **Surveillance**.
1. Glissez-déplacez la vignette du graphique sur votre tableau de bord pour la métrique à afficher. Répétez cette opération pour toutes les métriques à afficher sur le tableau de bord. Dans l’image suivante, le graphique « Objets Blob - Nombre total de requêtes » est mis en surbrillance comme exemple, mais tous les graphiques peuvent être placés sur votre tableau de bord.

   ![Galerie de vignettes dans le portail Azure](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Sélectionnez **Personnalisation terminée** en haut du tableau de bord lorsque vous avez terminé d’ajouter des graphiques.

Une fois que vous avez ajouté des graphiques à votre tableau de bord, vous pouvez les personnaliser davantage comme décrit dans Personnaliser les graphiques de métrique.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Storage Analytics, consultez [Storage Analytics](storage-analytics.md) pour Storage Analytics.
* [Configurez les journaux Storage Analytics](manage-storage-analytics-logs.md).
* En savoir plus sur le schéma des métriques. Consultez [Schéma de table des métriques Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).