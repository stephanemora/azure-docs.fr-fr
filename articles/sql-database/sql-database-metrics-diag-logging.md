---
title: Journalisation des métriques et diagnostics d’Azure SQL Database | Microsoft Docs
description: Découvrez comment configurer Azure SQL Database pour stocker les statistiques d’utilisation des ressources, de connectivité et d’exécution de requête.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 775883d575a87758f563bd8dae8e5a726cd8ed36
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49959075"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Journalisation des métriques et diagnostics d’Azure SQL Database 

Azure SQL Database, les pools de bases de données élastiques, Managed Instance et les bases de données de Managed Instance peuvent générer des métriques et des journaux de diagnostic pour faciliter l’analyse des performances. Vous pouvez configurer une base de données pour qu’elle diffuse en continu les informations relatives à l’utilisation des ressources, aux employés et aux sessions, ainsi qu’à la connectivité, dans l’une de ces ressources Azure :

* **Azure SQL Analytics** : utilisé comme une solution de supervision des performances intelligente, intégrée à une base de données Azure, et comprenant des fonctionnalités de création de rapports, d’alerte et d’atténuation.
* **Concentrateur d’événements Azure** : pour intégrer des données de télémétrie SQL Database à votre solution de surveillance personnalisée ou à vos pipelines très actifs.
* **Stockage Azure** : utilisé pour archiver des quantités importantes de données de télémétrie pour une partie du prix.

    ![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

Pour comprendre les catégories de journaux et de métriques qui sont prises en charge par les différents services Azure, pensez à lire la documentation suivante :

* [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Vue d’ensemble des journaux de diagnostics Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

 Dans une base de données, vous pouvez activer et gérer la journalisation des données de télémétrie liées aux métriques et aux diagnostics à l’aide de l’une des méthodes suivantes :

- Portail Azure
- PowerShell
- Azure CLI
- API REST Azure Monitor 
- Modèle Azure Resource Manager

Lorsque vous activez la journalisation des métriques et des diagnostics, vous devez spécifier la destination de ressource Azure dans laquelle les données sélectionnées doivent être collectées. Les options disponibles sont les suivantes :

- SQL Analytics
- Event Hubs
- Stockage 

Vous pouvez approvisionner une nouvelle ressource Azure ou sélectionner une ressource existante. Après avoir sélectionné une ressource, à l’aide de l’option Paramètres de diagnostic, vous devez spécifier les données à collecter. 

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Activer la journalisation des pools élastiques ou de Managed Instance

Les pools élastiques et les instances Managed Instance en tant que conteneurs de base de données ont leurs propres données de télémétrie de diagnostic qui ne sont pas activées par défaut. Notez que ces données de télémétrie sont distinctes des données de télémétrie de diagnostic de base de données. C’est pourquoi la diffusion en continu des données de télémétrie de diagnostic pour les pools élastiques et Managed Instance doit être configurée en plus des données de télémétrie de diagnostic de base de données, comme expliqué ci-dessous. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour les pools élastiques

Les données suivantes de télémétrie de diagnostic sont disponibles pour la collecte d’une ressource de pool élastique :

| Ressource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Pool élastique** | [Toutes les métriques](sql-database-metrics-diag-logging.md#all-metrics) : pourcentage eDTU/UC, limite eDTU/UC, pourcentage de lecture de données physiques, pourcentage d’écriture du journal, pourcentage de sessions, pourcentage de Workers, stockage, pourcentage de stockage, limite de stockage, pourcentage de stockage XTP. |

Pour activer la diffusion en continu des données de télémétrie de diagnostic pour une **ressource de pool élastique**, procédez comme suit :

- Accédez à la ressource de pool élastique dans le portail Azure.
- Sélectionnez **Paramètres de diagnostic**.
- Sélectionnez **Activer les diagnostics** si aucun paramètre précédent n’existe, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
- Tapez le nom du paramètre (pour votre propre référence).
- Sélectionnez la ressource vers laquelle diffuser des données de diagnostic en continu à partir du pool élastique : **Archive to storage account** (Archiver dans un compte de stockage), **Stream to an event hub** (Diffuser en continu vers un Event Hub) ou **Send to Log Analytics** (Envoyer vers Log Analytics).
- Si le service Log Analytics est sélectionné, sélectionnez **Configurer** et créez un espace de travail en sélectionnant **+Créer un espace de travail**, ou sélectionnez un espace de travail existant.
- Cochez la case correspondant aux données de télémétrie de diagnostic de pool élastique **AllMetrics**.
- Cliquez sur **Enregistrer**.

Répétez les étapes ci-dessus pour chaque pool élastique que vous souhaitez analyser.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour Managed Instance

Les données suivantes de télémétrie de diagnostic sont disponibles pour la collecte d’une ressource Managed Instance :

| Ressource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) : nombre de vCores, pourcentage d’UC moyenne, requêtes d’E/S, octets lus/écrits, espace de stockage réservé, espace de stockage utilisé. |

Pour activer la diffusion en continu des données de télémétrie de diagnostic pour une **ressource Managed Instance**, procédez comme suit :

- Accédez à la ressource Managed Instance dans le portail Azure.
- Sélectionnez **Paramètres de diagnostic**.
- Sélectionnez **Activer les diagnostics** si aucun paramètre précédent n’existe, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
- Tapez le nom du paramètre (pour votre propre référence).
- Sélectionnez la ressource vers laquelle diffuser des données de diagnostic en continu à partir du pool élastique : **Archive to storage account** (Archiver dans un compte de stockage), **Stream to an event hub** (Diffuser en continu vers un Event Hub) ou **Send to Log Analytics** (Envoyer vers Log Analytics).
- Si le service Log Analytics est sélectionné, créez ou utilisez un espace de travail existant.
- Cochez la case correspondant aux données de télémétrie de diagnostic d’instance **ResourceUsageStats**.
- Cliquez sur **Enregistrer**.

Répétez les étapes ci-dessus pour chaque instance Managed Instance que vous souhaitez analyser.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Activer la journalisation pour Azure SQL Database ou les bases de données dans Managed Instance

La journalisation des métriques et des diagnostics dans SQL Database et les bases de données de Managed Instance n’est pas activée par défaut.

Les données suivantes de télémétrie de diagnostic sont disponibles pour la collecte des instances Azure SQL Database et des bases de données dans Managed Instance :

| Analyse des données de télémétrie pour les bases de données | Prise en charge d’Azure SQL Database | Prise en charge des bases de données dans Managed Instance |
| :------------------- | ------------------- | ------------------- |
| [Toutes les métriques](sql-database-metrics-diag-logging.md#all-metrics) : Pourcentage DTU/UC, Limite DTU/UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP. | Oui | Non  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics) : contient des informations sur les statistiques d’exécution de requête telles que l’utilisation du processeur et la durée des requêtes. | Oui | Oui |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics) : contient des informations sur les statistiques d’attente des requêtes vous indiquant ce que vos requêtes ont attendu, comme CPU, LOG, LOCKING. | Oui | Oui |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset) : contient des informations sur les erreurs SQL qui se sont produites dans cette base de données. | Oui | Non  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset) : contient des informations sur le temps qu’une base de données a passé à attendre différents types d’attente. | Oui | Non  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset) : contient des informations sur les expirations du délai d’attente qui ont eu lieu sur une base de données. | Oui | Non  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset) : contient des informations sur les événements bloquants qui se sont produits dans une base de données. | Oui | Non  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset) : contient les informations Intelligent Insights relatives aux performances. [En savoir plus sur Intelligent Insights](sql-database-intelligent-insights.md). | Oui | Oui |

### <a name="azure-portal"></a>Portail Azure

La diffusion en continu des données de télémétrie de diagnostic pour Azure SQL Database et les bases de données dans Managed Instance vers les destinations stockage Azure, Event Hubs ou Log Analytics est configurée dans le menu Paramètres de diagnostic pour chacune des bases de données du portail Azure.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour Azure SQL Database

Pour activer la diffusion en continu des données de télémétrie de diagnostic pour **Azure SQL Database**, procédez comme suit :

- Accédez à la ressource Azure SQL Database.
- Sélectionnez **Paramètres de diagnostic**.
- Sélectionnez **Activer les diagnostics** si aucun paramètre précédent n’existe, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
- Jusqu’à trois (3) connexions parallèles permettant de diffuser en continu des données de télémétrie de diagnostic peuvent être créées. Pour configurer plusieurs diffusions en continu parallèles de données de diagnostic vers plusieurs ressources, sélectionnez **+Ajouter un paramètre de diagnostic** pour créer un paramètre supplémentaire.

   ![Activer dans le portail Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

- Tapez le nom du paramètre (pour votre propre référence).
- Sélectionnez la ressource vers laquelle diffuser des données de diagnostic en continu à partir de la base de données : **Archive to storage account** (Archiver dans un compte de stockage), **Stream to an event hub** (Diffuser en continu vers un Event Hub) ou **Send to Log Analytics** (Envoyer vers Log Analytics).
- Pour une expérience d’analyse standard, cochez les cases correspondant aux données de télémétrie de journal de diagnostic de base de données : **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**, **Deadlocks**. Ces données de télémétrie sont basées sur les événements et assurent l’expérience d’analyse standard.
- Pour une expérience d’analyse avancée, cochez la case **AllMetrics**. Il s’agit de données de télémétrie en 1 minute correspondant aux données de télémétrie de diagnostics de base de données décrites ci-dessus. 

   ![Paramètres de diagnostic](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

Répétez les étapes ci-dessus pour chaque instance Azure SQL Database que vous souhaitez analyser.

> [!NOTE]
> L’option Journal d’audit ne peut pas être activée à partir de Paramètres de diagnostic même si elle est affichée. Pour l’activer, consultez l’article [Set up auditing for your database](sql-database-auditing.md#subheading-2) (Configurer l’audit de votre base de données).
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour les bases de données dans Managed Instance

Pour activer la diffusion en continu des données de télémétrie de diagnostic pour les **bases de données dans Managed Instance**, procédez comme suit :

- Accédez à la base de données dans Managed Instance.
- Sélectionnez **Paramètres de diagnostic**.
- Sélectionnez **Activer les diagnostics** si aucun paramètre précédent n’existe, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
- Jusqu’à trois (3) connexions parallèles permettant de diffuser en continu des données de télémétrie de diagnostic peuvent être créées. Pour configurer plusieurs diffusions en continu parallèles de données de diagnostic vers plusieurs ressources, sélectionnez **+Ajouter un paramètre de diagnostic** pour créer un paramètre supplémentaire.
- Tapez le nom du paramètre (pour votre propre référence).
- Sélectionnez la ressource vers laquelle diffuser des données de diagnostic en continu à partir de la base de données : **Archive to storage account** (Archiver dans un compte de stockage), **Stream to an event hub** (Diffuser en continu vers un Event Hub) ou **Send to Log Analytics** (Envoyer vers Log Analytics).
- Cochez les cases correspondant aux données de télémétrie de diagnostic de base de données : **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** et **Errors**.

   ![Paramètres de diagnostic](./media/sql-database-metrics-diag-logging/diagnostics-portal-mi.png)

Répétez les étapes ci-dessus pour chaque base de données de Managed Instance que vous souhaitez analyser.

### <a name="powershell"></a>PowerShell

Pour activer la journalisation des métriques et diagnostics à l’aide de PowerShell, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage où vous souhaitez envoyer les journaux.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L’ID de règle Azure Service Bus est une chaîne au format suivant :

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="to-configure-multiple-azure-subscriptions"></a>Pour configurer plusieurs abonnements Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell).

Fournissez l’ID de ressource d’espace de travail &lt;$WSID&gt; en tant que paramètre lors de l’exécution du script (Enable-AzureRMDiagnostics.ps1) pour transmettre les données de plusieurs ressources d’un abonnement Azure vers l’espace de travail. Pour obtenir l’ID de l’espace de travail &lt;$WSID&gt; auquel vous souhaitez envoyer des données de diagnostic, remplacez &lt;subID&gt; avec l’ID d’abonnement, remplacez &lt;RG_NAME&gt; avec le nom de groupe de ressources et remplacez &lt;WS_NAME&gt; par le nom de l’espace de travail dans le script suivant.

- Pour configurer plusieurs ressources Azure, utilisez les commandes suivantes :

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Pour activer la journalisation des métriques et diagnostics à l’aide d’Azure CLI, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage où vous souhaitez envoyer les journaux.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L’ID de règle Service Bus est une chaîne au format suivant :

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="rest-api"></a>API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API RESTS Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Envoyer à Log Analytics 

Les journaux de métriques et diagnostics SQL Database peuvent être transmis en continu dans le Log Analytics à l’aide de l’option intégrée **Envoyer à Log Analytics** dans le portail. Vous pouvez également activer Log Analytics à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI Azure ou l’API REST d’Azure Monitor.

### <a name="installation-overview"></a>Vue d’ensemble de l’installation

La surveillance d’une flotte SQL Database est simple avec Log Analytics. Trois étapes sont requises :

1. Créez une ressource Log Analytics.

2. Configurez les bases de données pour enregistrer des journaux de métriques et diagnostics dans la ressource Log Analytics que vous avez créée.

3. Installez la solution **Azure SQL Analytics** à partir de la Place de marché Azure.

### <a name="create-a-log-analytics-resource"></a>Créer une ressource Log Analytics

1. Sélectionnez **Créer une ressource** dans le menu de gauche.

2. Sélectionnez **Surveillance et gestion**.

3. Sélectionnez **Log Analytics**.

4. Complétez le formulaire de Log Analytics avec les informations supplémentaires requises : nom d’espace de travail, abonnement, groupe de ressources, emplacement et niveau tarifaire.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurer des bases de données pour enregistrer des journaux de métriques et diagnostics

La manière la plus simple de configurer l’emplacement où les bases de données enregistrent leurs mesures est d’utiliser le portail Azure. Dans le portail, accédez aux ressources de votre base de données SQL et sélectionnez **Paramètres de diagnostic**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Installer la solution SQL Analytics à partir de la galerie

1. Une fois la ressource Log Analytics créée et vos données en circulation dans celle-ci, installez la solution SQL Analytics. Sur la page d’accueil, dans le menu latéral, sélectionnez **Galerie de solutions**. Dans la galerie, sélectionnez la solution **Azure SQL Analytics**, puis sélectionnez **Ajouter**.

   ![Solution de surveillance](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Sur votre page d’accueil, la vignette **Azure SQL Analytics** s’affiche. Sélectionnez cette vignette pour ouvrir le tableau de bord SQL Analytics.

### <a name="use-the-sql-analytics-solution"></a>Utiliser la solution SQL Analytics

SQL Analytics est un tableau de bord hiérarchique qui vous permet de naviguer dans la hiérarchie des ressources de SQL Database. Pour découvrir comment utiliser la solution SQL Analytics, consultez [Surveiller une base de données SQL à l’aide de la solution SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmission en continu vers Event Hubs

Les journaux de métriques et de diagnostics SQL Database peuvent être transmis en continu vers Event Hubs à l’aide de l’option intégrée **Transmettre en continu à un concentrateur d’événements** dans le portail. Vous pouvez également activer l’ID de règle Service Bus à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI Azure ou l’API REST d’Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Que faire des journaux de métriques et diagnostics dans Event Hubs
Après avoir sélectionné les données envoyées à Event Hub, vous vous rapprochez de l’activation de scénarios d’analyse avancée. Event Hubs fait office de porte d’entrée pour un pipeline d’événements. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Event Hubs dissocie la production d’un flux d’événements de la consommation de ces événements. De cette façon, les consommateurs d’événements peuvent accéder aux événements sur leur propre calendrier. Pour plus d’informations sur Event Hubs, consultez :

- [Qu’est-ce qu’Azure Event Hubs ?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Voici quelques façons d’utiliser la fonctionnalité de diffusion en continu :

* **Afficher l’intégrité du service par diffusion en continu des données de chemin réactif vers Power BI**. En utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de métriques et de diagnostic en informations en temps réel sur vos services Azure. Pour une vue d’ensemble de la manière de configurer un concentrateur d’événements, de traiter les données avec Stream Analytics, et d’utiliser PowerBI comme sortie, voir [Stream Analytics et Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Diffuser en continu des journaux vers des flux de journalisation et de données de télémétrie tiers**. À l’aide de la transmission en continu Event Hubs, vous pouvez obtenir vos journaux de diagnostics et de mesures dans différentes solutions d’analyse et de journalisation tierces. 

* **Créer une plateforme de télémétrie et de journalisation personnalisée**. Si vous disposez déjà d’une plateforme de télémétrie personnalisée ou que vous envisagez d’en créer une, la nature hautement évolutive de publication/abonnement nature d’Event Hubs vous permet de réceptionner avec souplesse les journaux de diagnostic. Lisez le [guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmission en continu dans le stockage

Les journaux de métriques et de diagnostics de base de données SQL peuvent être stockés dans le stockage à l’aide de l’option intégrée **Archiver dans un compte de stockage** dans le portail. Vous pouvez également activer le stockage à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI Azure ou l’API REST d’Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma des journaux de métriques et diagnostics dans le compte de stockage

Après que vous avez configuré la collecte des journaux de métriques et diagnostics, lorsque les premières lignes de données sont disponibles, un conteneur de stockage est créé dans le compte de stockage que vous avez sélectionné. La structure de ces objets blob est la suivante :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Ou, plus simplement :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Par exemple, un nom d’objet blob pour toutes les métriques pourrait être :

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Si vous souhaitez enregistrer les données du Pool élastique, le nom d’objet blob est un peu différent :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Télécharger les métriques et journaux du stockage

Découvrez comment [télécharger les journaux de métriques et de diagnostics à partir du stockage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Stratégie de rétention des données et tarification

Si vous sélectionnez Event Hubs ou un compte de stockage, vous pouvez spécifier une stratégie de rétention. Cette stratégie supprime les données antérieures à un intervalle de temps sélectionné. Si vous spécifiez Log Analytics, la stratégie de rétention dépend du niveau tarifaire sélectionné. La consommation des données de télémétrie de diagnostic qui dépasse les unités gratuites d’ingestion de données allouées chaque mois est facturée. Les unités gratuites d’ingestion de données fournies permettent de superviser gratuitement plusieurs bases de données chaque mois. Notez que les bases de données plus actives avec des charges de travail plus lourdes ingèrent plus de données que les bases de données inactives. Pour plus d’informations, consultez [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Si vous utilisez Azure SQL Analytics, vous pouvez facilement analyser votre consommation d’ingestion de données dans la solution en sélectionnant l’espace de travail OMS dans le menu de navigation d’Azure SQL Analytics, puis en sélectionnant Utilisation et estimation des coûts.

## <a name="metrics-and-logs-available"></a>Métriques et journaux disponibles

Voici le détail des données de télémétrie concernant l’analyse des métriques et des journaux qui sont disponibles pour Azure SQL Database, les pools élastiques, Managed Instance et les bases de données dans Managed Instance pour votre **analyse personnalisée** et le **développement d’applications** à l’aide du [langage SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="all-metrics"></a>Toutes les métriques

### <a name="all-metrics-for-elastic-pools"></a>Toutes les métriques des pools élastiques

|**Ressource**|**Métriques**|
|---|---|
|Pool élastique|Pourcentage DTU, eDTU utilisé, Limite eDTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Limite de stockage, Pourcentage de stockage XTP |

### <a name="all-metrics-for-azure-sql-database"></a>Toutes les métriques d’Azure SQL Database

|**Ressource**|**Métriques**|
|---|---|
|Azure SQL Database|Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP et blocages |

## <a name="logs"></a>Journaux

### <a name="logs-for-managed-instance"></a>Journaux Managed Instance

### <a name="resource-usage-stats"></a>Statistiques relatives à l’utilisation des ressources

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : ResourceUsageStats|
|Ressource|Nom de la ressource.|
|ResourceType|Nom du type de ressource. Toujours : MANAGEDINSTANCES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom de l’instance managée.|
|ResourceId|URI de ressource.|
|SKU_s|Référence SKU produit Managed Instance|
|virtual_core_count_s|Nombre de vCore disponibles|
|avg_cpu_percent_s|Pourcentage d’UC moyenne|
|reserved_storage_mb_s|Capacité de stockage réservée sur Managed Instance|
|storage_space_used_mb_s|Stockage utilisé sur Managed Instance|
|io_requests_s|Nombre d’IOPS|
|io_bytes_read_s|Octets d’IOPS lus|
|io_bytes_written_s|Octets d’IOPS écrits|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Journaux pour les bases de données Azure SQL Database et Managed Instance

### <a name="query-store-runtime-statistics"></a>Statistiques d’exécution du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|OperationName|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Resource|Nom de la ressource.|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|query_hash_s|Hachage de requête.|
|query_plan_hash_s|Hachage de plan de requête.|
|statement_sql_handle_s|Descripteur sql d’instruction.|
|interval_start_time_d|Démarrez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|interval_end_time_d|Terminez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|logical_io_writes_d|Nombre total d’écritures E/S logiques.|
|max_logical_io_writes_d|Nombre maximal d’écritures E/S logiques par exécution.|
|physical_io_reads_d|Nombre total de lectures E/S physiques.|
|max_physical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution.|
|logical_io_reads_d|Nombre total de lectures E/S logiques.|
|max_logical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution.|
|execution_type_d|Type d’exécution.|
|count_executions_d|Nombre d’exécutions de la requête.|
|cpu_time_d|Temps processeur total consommé par la requête, en microsecondes.|
|max_cpu_time_d|Consommateur de temps processeur maximal par une exécution unique en microsecondes.|
|dop_d|Somme des degrés du parallélisme.|
|max_dop_d|Degré maximal de parallélisme utilisé pour une seule exécution.|
|rowcount_d|Nombre total de lignes retournées.|
|max_rowcount_d|Nombre maximal de lignes retournées dans une seule exécution.|
|query_max_used_memory_d|Quantité totale de mémoire utilisée en Ko.|
|max_query_max_used_memory_d|Quantité maximale de mémoire utilisée par une exécution unique, en Ko.|
|duration_d|Durée d’exécution totale en microsecondes.|
|max_duration_d|Durée d’exécution maximal d’une seule exécution.|
|num_physical_io_reads_d|Nombre total de lectures physiques.|
|max_num_physical_io_reads_d|Nombre maximal de lectures physiques par exécution.|
|log_bytes_used_d|Quantité totale d’octets de journal utilisés.|
|max_log_bytes_used_d|Quantité maximale d’octets de journal utilisés par exécution.|
|query_id_d|ID de la requête dans le magasin des requêtes.|
|plan_id_d|ID du plan dans le magasin des requêtes.|

En savoir plus sur les [données de statistiques d’exécution du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiques d’attente du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : QueryStoreWaitStatistics|
|OperationName|Nom de l’opération. Toujours : QueryStoreWaitStatisticsEvent|
|Resource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|wait_category_s|Catégorie de l’attente.|
|is_parameterizable_s|Indique si la requête est paramétrable.|
|statement_type_s|Type de l’instruction.|
|statement_key_hash_s|Hachage de clé d’instruction.|
|exec_type_d|Type d’exécution.|
|total_query_wait_time_ms_d|Temps d’attente total de la requête sur la catégorie d’attente spécifique.|
|max_query_wait_time_ms_d|Temps d’attente maximal de la requête dans une exécution individuelle sur la catégorie d’attente spécifique.|
|query_param_type_d|0|
|query_hash_s|Hachage de requête dans le magasin des requêtes.|
|query_plan_hash_s|Hachage de plan de requêtes dans le magasin des requêtes.|
|statement_sql_handle_s|Descripteur d’instruction dans le magasin des requêtes.|
|interval_start_time_d|Démarrez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|interval_end_time_d|Terminez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|count_executions_d|Comptabilisation des exécutions de la requête.|
|query_id_d|ID de la requête dans le magasin des requêtes.|
|plan_id_d|ID du plan dans le magasin des requêtes.|

Découvrez-en davantage sur les [données des statistiques d’attente du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Jeu de données d’erreurs

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : Errors|
|OperationName|Nom de l’opération. Toujours : ErrorEvent|
|Resource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|Message|Message d’erreur en texte brut.|
|user_defined_b|Indique si l’erreur est un bit défini par l’utilisateur.|
|error_number_d|Code d’erreur.|
|Severity|Gravité de l’erreur.|
|state_d|État de l’erreur.|
|query_hash_s|Hachage de requête de la requête ayant échoué si disponible.|
|query_plan_hash_s|Hachage du plan de requête de la requête ayant échoué si disponible.|

En savoir plus sur les [messages d’erreur SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Jeu de données de statistiques d’attente de base de données

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours  : DatabaseWaitStatistics|
|OperationName|Nom de l’opération. Toujours : DatabaseWaitStatisticsEvent|
|Resource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|wait_type_s|Nom du type d’attente.|
|start_utc_date_t [UTC]|Heure de début de période mesuré.|
|end_utc_date_t [UTC]|Heure de fin de période mesuré.|
|delta_max_wait_time_ms_d|Temps d’attente maximal par exécution|
|delta_signal_wait_time_ms_d|Durée d’attente totale de signal.|
|delta_wait_time_ms_d|Durée d’attente totale dans la période.|
|delta_waiting_tasks_count_d|Nombre de tâches en attente.|

Apprenez-en davantage sur les [statistiques d’attente de base de données](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Jeu de données d’expirations

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : Timeouts|
|OperationName|Nom de l’opération. Toujours : TimeoutEvent|
|Resource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|error_state_d|Code d’état d’erreur.|
|query_hash_s|Hachage de requête si disponible.|
|query_plan_hash_s|Hachage de plan de requête si disponible.|

### <a name="blockings-dataset"></a>Jeu de données de blocages

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : Blocks|
|OperationName|Nom de l’opération. Toujours : BlockEvent|
|Resource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|lock_mode_s|Mode de verrouillage utilisé par la requête.|
|resource_owner_type_s|Propriétaire du verrou.|
|blocked_process_filtered_s|XML de rapport de processus bloqué.|
|duration_d|Durée du verrou en microsecondes.|

### <a name="deadlocks-dataset"></a>Jeu de données Deadlocks

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC] |Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : Deadlocks|
|OperationName|Nom de l’opération. Toujours : DeadlockEvent|
|Ressource|Nom de la ressource.|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données. |
|ResourceId|URI de ressource.|
|deadlock_xml_s|Rapport de blocage XML.|

### <a name="automatic-tuning-dataset"></a>Jeu de données AutomaticTuning

|Propriété|Description|
|---|---|
|TenantId|Votre ID de client.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Category|Nom de la catégorie. Toujours : AutomaticTuning|
|Ressource|Nom de la ressource.|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|LogicalDatabaseName_s|Nom de la base de données.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données.|
|ResourceId|URI de ressource.|
|RecommendationHash_s|Hachage unique de la recommandation de réglage automatique.|
|OptionName_s|Opération de réglage automatique.|
|Schema_s|Schéma de la base de données.|
|Table_s|Table concernée.|
|IndexName_s|Nom de l’index.|
|IndexColumns_s|Nom de la colonne.|
|IncludedColumns_s|Colonnes incluses.|
|EstimatedImpact_s|Impact estimé de la recommandation de réglage automatique JSON.|
|Event_s|Type d’événement de réglage automatique.|
|Timestamp_t|Timestamp de la dernière mise à jour.|

### <a name="intelligent-insights-dataset"></a>Jeu de données Intelligent Insights
Apprenez-en davantage sur le [format de journal Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer la journalisation et comprendre les catégories de journaux et métriques prises en charge par les différents services Azure, consultez :

 * [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Vue d’ensemble des journaux de diagnostics Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Pour plus d’informations sur les concentrateurs d’événements, lisez :

* [Nouveautés des concentrateurs d’événements Azure ?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Pour en savoir plus sur le stockage, découvrez comment [télécharger les journaux de métriques et de diagnostics à partir du stockage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
