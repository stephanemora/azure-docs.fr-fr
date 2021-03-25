---
title: Activation et gestion des journaux Azure Storage Analytics (classique) | Microsoft Docs
description: Découvrez comment superviser un compte de stockage dans Azure avec Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701703"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Activation et gestion des journaux Azure Storage Analytics (classique)

[Azure Storage Analytics](storage-analytics.md) fournit des journaux pour les objets blob, les files d’attente et les tables. Vous pouvez utiliser le [Portail Azure](https://portal.azure.com) afin de configurer les journaux à enregistrer pour votre compte. Cet article explique comment activer et gérer les journaux. Pour savoir comment activer les métriques, consultez [Activation et gestion des métriques Azure Storage Analytics (classique)]().  L’examen et le stockage des données de supervision sur le Portail Azure occasionne des frais. Pour plus d’informations, consultez [Storage Analytics](storage-analytics.md).

> [!NOTE]
> Nous vous recommandons d’utiliser les journaux Stockage Azure disponibles dans Azure Monitor au lieu des journaux Storage Analytics. Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Cette préversion active les journaux des objets blob (qui incluent Azure Data Lake Storage Gen2), les fichiers, les files d’attente et les tables. Pour plus d’informations, consultez l’un des articles suivants :
>
> - [Supervision du Stockage Blob Azure](../blobs/monitor-blob-storage.md)
> - [Supervision d’Azure Files](../files/storage-files-monitoring.md)
> - [Surveiller le service Stockage File d'attente](../queues/monitor-queue-storage.md)
> - [Supervision du stockage Table Azure](../tables/monitor-table-storage.md)

Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Activer les journaux d’activité

Vous pouvez demander au Stockage Azure d’enregistrer les journaux de diagnostic pour les requêtes de lecture, d’écriture et de suppression pour les services blob, table et file d’attente. La stratégie de rétention des données que vous définissez s’applique également à ces journaux d’activité.

> [!NOTE]
> Azure Files prend actuellement en charge les métriques Storage Analytics, mais non la journalisation Storage Analytics.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, puis le nom du compte de stockage pour ouvrir le panneau du compte de stockage.

2. Sélectionnez **Paramètres de diagnostic (classique)** dans la section **Supervision (classique)** du panneau du menu.

    ![Élément de menu Diagnostics sous SURVEILLANCE dans le portail Azure.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Vérifiez que **l’état** est défini sur **On**, puis sélectionnez les **services** pour lesquels vous souhaitez activer la journalisation.

   > [!div class="mx-imgBorder"]
   > ![Configurez la journalisation dans le portail Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Vérifiez que la case **Supprimer les données** est cochée.  Ensuite, définissez le nombre de jours pendant lesquels vous souhaitez conserver les données de journal en déplaçant la commande de curseur sous la case à cocher, ou en modifiant directement la valeur qui apparaît dans la zone de texte à côté de la commande de curseur. La valeur par défaut pour les nouveaux comptes de stockage est de sept jours. Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de journal.

   > [!WARNING]
   > Les journaux sont stockés sous forme de données dans votre compte. Les données de journal peuvent s’accumuler dans votre compte au fil du temps, ce qui risque d’augmenter le coût du stockage. Si vous n’avez besoin de ces données que pendant une durée limitée, vous pouvez réduire vos coûts en modifiant la stratégie de rétention des données. Les données de journal périmées (antérieures à votre stratégie de rétention des données) sont supprimées par le système. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation souhaitée des données de journal de votre compte. Pour plus d’informations, consultez la page [Facturation sur les métriques de stockage](storage-analytics-metrics.md#billing-on-storage-metrics).

4. Cliquez sur **Enregistrer**.

   Les journaux de diagnostic sont enregistrés dans un conteneur d’objets blob nommé *$logs* dans votre compte de stockage. Vous pouvez afficher les données du journal à l’aide d’un explorateur de stockage, tel que l’[Explorateur Stockage Microsoft Azure](https://storageexplorer.com), ou de façon programmatique à l’aide d’une bibliothèque de client de stockage ou PowerShell.

   Pour plus d’informations sur l’accès au conteneur $logs, consultez la page [Journalisation Storage Analytics](storage-analytics-logging.md).

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

6. Utilisez **Set-AzStorageServiceLoggingProperty** pour changer les paramètres des journaux actuels. Les cmdlets qui contrôlent la journalisation du stockage utilisent un paramètre **LoggingOperations**, une chaîne contenant une liste de types de requêtes à consigner séparée par des virgules. Les trois types de requêtes possibles sont **lire**, **écrire** et **supprimer**. Pour désactiver la journalisation, utilisez la valeur **aucun** pour le paramètre **LoggingOperations**.  

   La commande suivante active la journalisation des requêtes de lecture, d’écriture et de suppression dans le service de File d’attente de votre compte de stockage par défaut avec une rétention de cinq jours :  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Les journaux sont stockés sous forme de données dans votre compte. Les données de journal peuvent s’accumuler dans votre compte au fil du temps, ce qui risque d’augmenter le coût du stockage. Si vous n’avez besoin de ces données que pendant une durée limitée, vous pouvez réduire vos coûts en modifiant la stratégie de rétention des données. Les données de journal périmées (antérieures à votre stratégie de rétention des données) sont supprimées par le système. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation souhaitée des données de journal de votre compte. Pour plus d’informations, consultez la page [Facturation sur les métriques de stockage](storage-analytics-metrics.md#billing-on-storage-metrics).
   
   La commande suivante désactive la journalisation du service de table dans votre compte de stockage par défaut :  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Pour plus d’informations sur la configuration des applets de commande Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, consultez : [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Modification de la période de rétention des données de journal

Les données de journal peuvent s’accumuler dans votre compte au fil du temps, ce qui risque d’augmenter le coût du stockage. Si vous n’avez besoin de ces données que pendant une durée limitée, vous pouvez réduire vos coûts en modifiant la période de rétention des données de journal. Par exemple, si vous n’avez besoin de journaux que pendant trois jours, définissez cette période sur `3`. Ainsi, les journaux sont automatiquement supprimés de votre compte au bout de trois jours. Cette section vous montre comment afficher la période actuelle de rétention des données de journal, puis la mettre à jour si nécessaire.

> [!NOTE]
> Cette procédure s’applique uniquement aux comptes pour lesquels le paramètre **Espace de noms hiérarchique** est activé. Si vous avez activé ce paramètre sur votre compte, le paramètre des jours de rétention n’est pas encore pris en charge. Vous devrez donc supprimer les journaux manuellement avec n’importe quel outil pris en charge, comme l’Explorateur Stockage Azure, REST ou un kit SDK. Pour rechercher ces journaux dans votre compte de stockage, consultez [Mode de stockage des journaux](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, puis le nom du compte de stockage pour ouvrir le panneau du compte de stockage.
2. Sélectionnez **Paramètres de diagnostic (classique)** dans la section **Supervision (classique)** du panneau du menu.

    ![Élément de menu Diagnostics sous SUPERVISION sur le Portail Azure](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Vérifiez que la case **Supprimer les données** est cochée.  Ensuite, définissez le nombre de jours pendant lesquels vous souhaitez conserver les données de journal en déplaçant la commande de curseur sous la case à cocher, ou en modifiant directement la valeur qui apparaît dans la zone de texte à côté de la commande de curseur.

   > [!div class="mx-imgBorder"]
   > ![Modification de la période de rétention sur le Portail Azure](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Le nombre de jours par défaut pour les nouveaux comptes de stockage est de sept jours. Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance.
   
4. Cliquez sur **Enregistrer**.

   Les journaux de diagnostic sont enregistrés dans un conteneur d’objets blob nommé *$logs* dans votre compte de stockage. Vous pouvez afficher les données du journal à l’aide d’un explorateur de stockage, tel que l’[Explorateur Stockage Microsoft Azure](https://storageexplorer.com), ou de façon programmatique à l’aide d’une bibliothèque de client de stockage ou PowerShell.

   Pour plus d’informations sur l’accès au conteneur $logs, consultez la page [Journalisation Storage Analytics](storage-analytics-logging.md).

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

5. Récupérez le contexte du compte de stockage qui définit le compte de stockage.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage. 

6. Utilisez [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) pour afficher la stratégie actuelle de rétention des journaux. L’exemple suivant imprime sur la console la période de rétention des services de stockage Blob et File d’attente.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   Dans la sortie de la console, la période de rétention s’affiche sous l’en-tête de colonne `RetentionDays`.

   > [!div class="mx-imgBorder"]
   > ![Stratégie de rétention dans la sortie PowerShell](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Utilisez [Set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) pour changer la période de rétention. Dans l’exemple suivant, elle est remplacée par quatre jours.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Pour plus d’informations sur la configuration des applets de commande Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, consultez : [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

L’exemple suivant imprime sur la console la période de rétention des services de stockage Blob et File d’attente.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Dans l’exemple suivant, elle est remplacée par quatre jours. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

L’exemple suivant imprime sur la console la période de rétention des services de stockage Blob et File d’attente.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Dans l’exemple suivant, la période de rétention des journaux des services de stockage Blob et File d’attente est remplacée par quatre jours. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Vérification de la suppression des données de journal

Vous pouvez vérifier que les journaux sont en cours de suppression en affichant le contenu du conteneur `$logs` de votre compte de stockage. L’illustration suivante montre le contenu d’un dossier dans le conteneur `$logs`. Le dossier correspond à janvier 2021, et chaque dossier contient les journaux d’une journée. Si nous sommes le 29 janvier 2021 et que votre stratégie de rétention est définie sur un seul jour, ce dossier ne contient les journaux que d’une journée.

> [!div class="mx-imgBorder"]
> ![Liste des dossiers de journaux sur le Portail Azure](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Affichage des données de journal

 Pour afficher et analyser vos données de journal, vous devez télécharger les objets Blob qui contiennent les données de journal souhaitées sur un ordinateur local. De nombreux outils de consultation du stockage vous permettent de télécharger des objets Blob de votre compte de stockage. Vous pouvez également utiliser l’outil de copie Azure de ligne de commande fourni par l’équipe de Stockage Azure [AzCopy](storage-use-azcopy-v10.md) pour télécharger vos données de journal.  
 
>[!NOTE]
> Le conteneur `$logs` n’étant pas intégré avec Event Grid, vous ne recevez pas de notifications lors de l’écriture de fichiers journaux. 

 Pour vous assurer de télécharger les données de journal souhaitées et éviter de télécharger les mêmes données plusieurs fois :  

-   Utilisez la convention d’affectation de noms de l’horodatage pour les objets Blob contenant les données de journal pour suivre les objets Blob que vous avez déjà téléchargés pour analyse afin d’éviter de télécharger plusieurs fois les mêmes données.  

-   Utilisez les métadonnées des objets Blob contenant des données de journal pour identifier la période pendant laquelle l’objet Blob conserve les données de journal afin d’identifier l’objet Blob exact que vous devez télécharger.  

Pour commencer à utiliser AzCopy, consultez [Prise en main d’AzCopy](storage-use-azcopy-v10.md) 

L’exemple suivant montre comment vous pouvez télécharger les données de journal du service de file d’attente pour les heures en commençant à 9h, 10h et 11h le 20 mai 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Pour plus d’informations sur le téléchargement de fichiers spécifiques, consultez [Téléchargement d’objets blob à partir du Stockage Blob Azure avec AzCopy v10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Lorsque vous avez téléchargé vos données de journal, vous pouvez afficher les entrées de journal dans les fichiers. Ces fichiers journaux utilisent un format de texte délimité que de nombreux outils de lecture de journaux sont en mesure d’analyser (pour plus d’informations, consultez le guide [Surveiller, diagnostiquer et résoudre les problèmes liés au service Stockage Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Différents outils possèdent des fonctionnalités différentes de mise en forme, de filtrage, de tri et de recherche de contenu de vos fichiers journaux. Pour plus d’informations sur le format du fichier journal de journalisation du stockage et son contenu, consultez [Format du journal de l’analyse de stockage](/rest/api/storageservices/storage-analytics-log-format) et [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Storage Analytics, consultez [Storage Analytics](storage-analytics.md) pour Storage Analytics.
* Pour plus d’informations sur l’utilisation d’un langage .NET pour configurer la journalisation du stockage, consultez la [référence sur la bibliothèque cliente de stockage](/previous-versions/azure/dn261237(v=azure.100)). 
* Pour obtenir des informations générales sur la configuration de la journalisation du stockage avec l’API REST, consultez [Activation et configuration de Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Pour plus d’informations sur le format des journaux Storage Analytics, consultez [Format des journaux Storage Analytics](/rest/api/storageservices/storage-analytics-log-format).
