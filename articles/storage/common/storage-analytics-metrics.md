---
title: Mesure analytique du stockage Azure (classique)
description: Découvrez comment utiliser des mesures dans le stockage Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: c15242b0c480e2da39897b850ab7b2a2fd05bf11
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489281"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Mesure analytique du stockage Azure (classique)

Storage Analytics peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage. Les transactions sont indiquées au niveau de l'opération d'API ainsi qu'au niveau du service de stockage, et la capacité est indiquée au niveau du service de stockage. Les données de métriques peuvent être utilisées pour analyser l'utilisation du service de stockage, diagnostiquer les problèmes au niveau des demandes effectuées auprès du service de stockage et améliorer les performances des applications qui utilisent un service.  

 Les métriques de Storage Analytics sont activées par défaut pour les nouveaux comptes de stockage. Vous pouvez configurer des métriques dans le [Azure portal](https://portal.azure.com/); pour plus d’informations, consultez [surveiller un compte de stockage dans le portail Azure](/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer l’Analytique de stockage pour chaque service.  

> [!NOTE]
> Mesures d’Analytique de stockage sont disponibles pour les services Blob, file d’attente, Table et fichier.
> Stockage Analytique métriques sont maintenant les métriques classique. Microsoft recommande d’utiliser [métriques de stockage dans Azure Monitor](storage-metrics-in-azure-monitor.md) au lieu de métriques de stockage Analytique.

## <a name="transaction-metrics"></a>Métriques de transaction  
 Un ensemble fiable de données est enregistré toutes les heures ou chaque minute pour chaque service de stockage et opération d'API demandée, notamment les entrées/sorties, la disponibilité, les erreurs et les pourcentages de demande triés par catégorie. La liste complète des détails de transaction est disponible dans la rubrique [Schéma de table de métriques Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Les données de transaction sont enregistrées à deux niveaux : au niveau du service et au niveau de l'opération API. Au niveau du service, des statistiques résumant toutes les opérations d'API demandées sont écrites dans une entité de table toutes les heures même si aucune demande n'a été adressée au service. Au niveau de l'opération d'API, les statistiques sont écrites uniquement dans une entité si l'opération a été demandée dans l'heure.  

 Par exemple, si vous exécutez une opération **GetBlob** sur votre service BLOB, les métriques Storage Analytics enregistrent la demande et l’incluent dans les données agrégées pour le service BLOB et l’opération **GetBlob**. Toutefois, si aucun **GetBlob** opération est demandée pendant l’heure, une entité ne sera pas écrite dans le *$MetricsTransactionsBlob* pour cette opération.  

 Les métriques de transaction sont enregistrées pour les demandes utilisateur et les demandes effectuées par Storage Analytics. Par exemple, les demandes par Storage Analytics pour écrire des journaux d’activité et des entités de table sont enregistrées.

## <a name="capacity-metrics"></a>Métriques de capacité  

> [!NOTE]
>  Actuellement, les métriques de capacité sont disponibles uniquement pour le service BLOB.

 Les données de capacité sont enregistrées quotidiennement pour le service BLOB d'un compte de stockage et deux entités de table sont écrites. Une entité fournit des statistiques sur les données utilisateur et l'autre, sur le conteneur d'objets blob `$logs` utilisé par Storage Analytics. Le *$MetricsCapacityBlob* table inclut les statistiques suivantes :  

- **Capacity** : La quantité de stockage utilisé par le service Blob du compte de stockage, en octets.  
- **ContainerCount** : Le nombre de conteneurs d’objets blob dans le service Blob du compte de stockage.  
- **ObjectCount** : Le nombre de blobs de blocs ou de pages validés et non validés dans le service Blob du compte de stockage.  

  Pour plus d'informations sur les métriques de capacité, consultez [Schéma de table de métriques Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Stockage des métriques  

 Toutes les données de métriques pour chacun des services de stockage sont stockées dans trois tables réservées à ce service : une table pour les informations sur les transactions, une autre pour les informations sur les transactions par minute et une troisième pour les informations sur la capacité. Les informations relatives aux transactions et aux transactions par minute se composent des données de demande et de réponse, et les informations de capacité se composent des données d'utilisation du stockage. Les métriques par heure, métriques par minute et la capacité pour un stockage Blob du compte de service est accessible dans les tables nommées comme indiqué dans le tableau suivant.  

|Niveau de métriques|Noms de table|Prise en charge pour les Versions|  
|-------------------|-----------------|----------------------------|  
|Métriques toutes les heures, emplacement principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versions antérieures à la version du 15 août 2013 uniquement. Bien que ces noms soient toujours pris en charge, il est recommandé d'utiliser les tables répertoriées ci-dessous.|  
|Métriques toutes les heures, emplacement principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Toutes les versions. Prise en charge pour les métriques de service de fichier est disponible uniquement dans la version 2015-04-05 et versions ultérieures.|  
|Métriques par minute, emplacement principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Toutes les versions. Prise en charge pour les métriques de service de fichier est disponible uniquement dans la version 2015-04-05 et versions ultérieures.|  
|Métriques toutes les heures, emplacement secondaire|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Toutes les versions. La géo-réplication redondante avec accès en lecture doit être activée.|  
|Métriques par minute, emplacement secondaire|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Toutes les versions. La géo-réplication redondante avec accès en lecture doit être activée.|  
|Capacité (service Blob uniquement)|$MetricsCapacityBlob|Toutes les versions.|  

 Ces tables sont créées automatiquement lorsque stockage Analytique est activé pour un point de terminaison de service de stockage. Elles sont accessibles par le biais de l’espace de noms du compte de stockage, par exemple : `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Les tables de métriques n’apparaissent pas dans une opération de liste et doivent être accessible directement via le nom de table.  

## <a name="enable-metrics-using-the-azure-portal"></a>Activer les métriques à l’aide du portail Azure
Pour activer les mesures dans le [Portail Azure](https://portal.azure.com), suivez les étapes suivantes :

1. Accédez à votre compte de stockage.
1. Sélectionnez **les paramètres de diagnostic (classique)** à partir de la **Menu** volet.
1. Vérifiez que l’option **État** est définie sur **Activé**.
1. Sélectionnez les métriques des services que vous souhaitez surveiller.
1. Spécifiez une stratégie de rétention pour indiquer la durée de conservation des métriques et de journalisation des données.
1. Sélectionnez **Enregistrer**.

Le [Portail Azure](https://portal.azure.com) ne vous permet pas de configurer des métriques par minute dans votre compte de stockage ; vous devez les activer avec PowerShell ou par programmation.

> [!NOTE]
>  Notez que le portail Azure ne pas actuellement vous permettent de configurer les métriques par minute dans votre compte de stockage. Vous devez activer les métriques par minute à l’aide de PowerShell ou par programmation.

## <a name="enable-storage-metrics-using-powershell"></a>Activer les métriques de stockage à l’aide de PowerShell  
Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer Storage Metrics dans votre compte de stockage à l’aide de l’applet de commande Azure PowerShell **Get-AzureStorageServiceMetricsProperty** pour récupérer les paramètres actuels et l’applet de commande  **Set-AzureStorageServiceMetricsProperty** pour modifier les paramètres actuels.  

Les applets de commande qui contrôlent Storage Metrics utilisent les paramètres suivants :  

* **ServiceType**, les valeurs possibles sont **Blob**, **file d’attente**, **Table**, et **fichier**.
* **MetricsType**, les valeurs possibles sont **heure** et **Minute**.  
* **MetricsLevel**, les valeurs possibles sont :
* **Aucun** : Désactive la surveillance.
* **Service** : Collecte des métriques telles que les entrées/sorties, la disponibilité, latence et les pourcentages de réussite, qui sont agrégées pour l’objet blob, file d’attente, table et services de fichiers.
* **ServiceAndApi**: Outre les métriques de Service, collecte le même ensemble de métriques pour chaque opération de stockage de l’API de service de stockage Azure.

Par exemple, la commande suivante active les métriques par minute pour le service BLOB dans votre compte de stockage par défaut avec une période de rétention de cinq jours :  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

La commande suivante récupère le niveau actuel des métriques par heure et la période de rétention en jours pour le service BLOB dans votre compte de stockage par défaut :  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Pour plus d’informations sur la configuration des applets de commande Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, consultez : [Guide pratique pour installer et configurer Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Activer Storage metrics par programmation  
Outre l’utilisation du portail Azure ou les applets de commande Azure PowerShell pour contrôler Storage Metrics, vous pouvez également utiliser une des API de stockage Azure. Par exemple, si vous utilisez un langage .NET, vous pouvez utiliser la bibliothèque cliente de stockage.  

Les classes **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, et **CloudFileClient** contiennent toutes des méthodes telles que  **SetServiceProperties** et **SetServicePropertiesAsync** qui prennent un **ServiceProperties** objet en tant que paramètre. Vous pouvez utiliser la **ServiceProperties** objet pour configurer Storage Metrics. Par exemple, ce qui suit C# extrait de code montre comment modifier les jours de rétention et de niveau de métriques pour les métriques par heure de la file d’attente :  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Pour plus d’informations sur l’utilisation d’un langage .NET pour configurer Storage Metrics, consultez [bibliothèque cliente Storage pour .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Pour obtenir des informations générales sur la configuration des métriques de stockage à l’aide de l’API REST, consultez [activation et configuration d’une Analytique de stockage](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Affichage des métriques de stockage  
Après que vous avez configuré les métriques d’analyse du stockage pour surveiller votre compte de stockage, l’analyse du stockage enregistre les métriques dans des tables connues dans votre compte de stockage. Vous pouvez configurer des graphiques permettant de consulter des mesures horaires dans le [Portail Azure](https://portal.azure.com) :

1. Accédez à votre compte de stockage dans le [Portail Azure](https://portal.azure.com).
1. Sélectionnez **métriques (classique)** dans le **Menu** panneau pour le service dont vous souhaitez afficher les mesures.
1. Cliquez sur le graphique que vous souhaitez configurer.
1. Dans le panneau **Modifier le graphique**, sélectionnez **l’Intervalle de temps**, le **Type de graphique** et les mesures que vous souhaitez afficher dans le graphique.

Dans le **Monitoring (classique)** section du Panneau de menu de votre compte de stockage dans le portail Azure, vous pouvez configurer [règles d’alerte](#metrics-alerts), telles que l’envoi de courrier électronique les alertes pour vous avertir lorsqu’une métrique spécifique atteint un valeur donnée.

Si vous souhaitez télécharger les métriques de stockage à long terme ou pour les analyser localement, vous devez utiliser un outil ou écrire du code pour lire les tables. Vous devez télécharger les métriques par minute pour analyse. Les tables ne sont pas visibles si vous répertoriez toutes les tables dans votre compte de stockage, mais vous pouvez y accéder directement par nom. De nombreux outils de consultation du stockage sont conscients de ces tables et vous permettent de les afficher directement (consultez [outils clients Azure Storage](/azure/storage/storage-explorers) pour obtenir la liste des outils disponibles).

||||  
|-|-|-|  
|**Mesures**|**Noms de tables**|**Notes**|  
|Métriques toutes les heures|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Dans les versions antérieures à 2013-08-15, ces tables portaient les :<br /><br /> $MetricsTransactionsBlob <br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Métriques pour le service de fichier sont disponibles depuis la version 2015-04-05.|  
|Métriques par minute|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Ne peut être activée à l’aide de PowerShell ou par programmation.<br /><br /> Métriques pour le service de fichier sont disponibles depuis la version 2015-04-05.|  
|Capacité|$MetricsCapacityBlob|Service BLOB uniquement.|  

Vous trouverez des informations complètes sur les schémas de ces tables dans [Schéma de table de métriques Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). Les exemples de lignes ci-dessous montrent uniquement un sous-ensemble des colonnes disponibles, mais ils illustrent les différentes façons dont Storage Metrics enregistre ces métriques :  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilité**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5.|5.|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6.|100|  

Dans cet exemple de données de métriques par minute, la clé de partition (PartitionKey) utilise une résolution d’une minute. La clé de ligne (RowKey) identifie le type d’informations qui sont stockées dans la ligne. Elle se compose de deux éléments : le type d’accès et le type de demande.  

-   Le type d’accès est soit **utilisateur** ou **système**, où **utilisateur** fait référence à toutes les demandes d’utilisateur pour le service de stockage, et **système** fait référence à demandes effectuées par l’Analytique de stockage.  

-   Le type de demande est **tous les** auquel cas il est une ligne de résumé, ou il identifie l’API spécifique comme **QueryEntity** ou **UpdateEntity**.  

Les exemples de données ci-dessus montrent tous les enregistrements pour une seule minute (à partir de 11 h 00), par conséquent, le nombre de **QueryEntities** demande plus le nombre de **QueryEntity** demande plus le nombre de  **UpdateEntity** demandes ajouter jusqu'à sept, ce qui correspond au total indiqué sur la **user : All** ligne. De même, vous pouvez dériver la latence de bout en bout moyenne (104,4286) sur le **user : All** ligne en calculant ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Alertes liées aux métriques
Vous devez envisager de définir des alertes dans le [Azure portal](https://portal.azure.com) donc vous êtes automatiquement averti des modifications importantes dans le comportement de vos services de stockage. Si vous utilisez un outil Explorateur de stockage pour télécharger ces données dans un format délimité, vous pouvez utiliser Microsoft Excel pour les analyser. Pour obtenir la liste des outils d’exploration du stockage disponibles, consultez [Outils clients Azure Storage](/azure/storage/storage-explorers) . Vous pouvez configurer des alertes dans le **alerte (classique)** panneau, accessible sous **Monitoring (classique)** dans le panneau de menu de compte de stockage.

> [!IMPORTANT]
> Il peut y avoir un décalage entre un événement de stockage et l’enregistrement des données métriques par heure ou par minute correspondantes. Dans le cas des métriques par minute, plusieurs minutes de données peuvent être écrites à la fois. Cela peut entraîner des transactions l’agrégation de minutes précédentes dans la transaction pour la minute actuelle. Dans ce cas, le service d’alerte peut ne pas disposer de toutes les données de métriques disponibles pour l’intervalle d’alerte configuré, ce qui peut conduire à des déclenchements d’alerte inattendus.
>

## <a name="accessing-metrics-data-programmatically"></a>Accès aux données de métriques par programmation  
L’exemple de code suivant en C# accède aux métriques par minute pour une plage de minutes et affiche les résultats dans une fenêtre de console. L’exemple de code utilise la version de la bibliothèque cliente Azure Storage 4.x ou version ultérieure, qui inclut le **CloudAnalyticsClient** classe qui simplifie l’accès aux tables de métriques de stockage.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
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

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
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

## <a name="billing-on-storage-metrics"></a>Facturation des métriques de stockage  
Les demandes d’écriture pour créer des entités de table pour les métriques sont facturées au tarif standard applicable à toutes les opérations Azure Storage.  

Demandes de lecture et de suppression de données de métriques par un client sont également facturées au tarif standard. Si vous avez configuré une stratégie de rétention des données, vous n’êtes pas facturé quand Azure Storage supprime les anciennes données de métriques. Toutefois, si vous supprimez des données de métriques, les opérations de suppression sont facturées à votre compte.  

La capacité utilisée par les tables de métriques est également facturable. Vous pouvez utiliser les éléments suivants pour estimer la quantité de capacité utilisée pour stocker les données de métriques :  

-   Si toutes les heures, un service qui utilise toutes les API de chaque service, environ 148 Ko de données sont stockée toutes les heures dans les tables de transaction de métriques si vous avez activé les deux service API niveau et résumé.  
-   Si, dans chaque heure, un service qui utilise des API dans le service, puis environ 12 Ko de données sont stockée toutes les heures dans les tables de transaction de métriques si vous avez activé le résumé de simplement au niveau du service.  
-   La table de capacité pour les objets BLOB a deux lignes supplémentaires sont ajoutées chaque jour, vous avez choisi de pour les journaux. Cela implique que tous les jours, la taille de cette table augmente par environ 300 octets au maximum.

## <a name="next-steps"></a>Étapes suivantes
* [Guide pratique pour surveiller un compte de stockage](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma de Table de métriques Storage Analytique](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Analytique de stockage opérations journalisées et Messages d’état](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Journalisation du stockage Analytique](storage-analytics-logging.md)
