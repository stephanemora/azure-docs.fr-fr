---
title: Journalisation des diagnostics et métriques Azure SQL Database | Microsoft Docs
description: Apprenez à configurer Azure SQL Database pour stocker les statistiques d’utilisation des ressources et d’exécution des requêtes.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 245372b39f4b730128c593ddd04225bd82875393
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387095"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Journalisation des diagnostics et métriques Azure SQL Database

Azure SQL Database, les pools élastiques, Managed Instance et les bases de données de Managed Instance peuvent diffuser en continu des métriques et des journaux de diagnostic pour faciliter la supervision des performances. Vous pouvez configurer une base de données de sorte qu’elle transmette les informations relatives à l’utilisation des ressources, aux workers et sessions ainsi qu’à la connectivité à l’une de ces ressources Azure :

* **Azure SQL Analytics** : pour bénéficier d’une supervision intelligente de vos bases de données Azure avec des rapports de performances, des alertes et des recommandations d’atténuation.
* **Azure Event Hubs** : pour intégrer des données de télémétrie SQL Database à votre solution de supervision personnalisée ou à vos pipelines chauds.
* **Stockage Azure** : pour archiver des quantités importantes de données de télémétrie pour une fraction du prix.

    ![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

Pour plus d’informations sur les métriques et les catégories de journal prises en charge par les divers services Azure, consultez :

* [Vue d’ensemble des métriques dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Vue d’ensemble des journaux de diagnostics Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Cet article fournit des conseils qui vous aideront à activer la télémétrie de diagnostic pour les bases de données, les pools élastiques et Managed Instance. Il peut aussi vous aider à comprendre comment configurer Azure SQL Analytics comme outil de supervision pour consulter les données de télémétrie de diagnostic de base de données.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Activer la journalisation des données de télémétrie de diagnostic

Vous pouvez activer et gérer la journalisation des métriques et des données de télémétrie de diagnostic à l’aide de l’une des méthodes suivantes :

- Portail Azure
- PowerShell
- Azure CLI
- API REST Azure Monitor
- Modèle Azure Resource Manager

Quand vous activez les métriques et la journalisation des diagnostics, vous devez spécifier la destination des ressources Azure pour la collecte des données de télémétrie de diagnostic. Les options disponibles sont les suivantes :

- Azure SQL Analytics
- Azure Event Hubs
- Stockage Azure

Vous pouvez approvisionner une nouvelle ressource Azure ou sélectionner une ressource existante. Après avoir choisi une ressource à l’aide de l’option **Paramètres de diagnostic**, spécifiez les données à collecter.

> [!NOTE]
> Si vous utilisez aussi des pools élastiques ou Managed Instance, nous vous recommandons également d’activer la télémétrie de diagnostic pour ces ressources. Les conteneurs de base de données présents dans les pools élastiques et Managed Instance disposent de leurs propres données de télémétrie de diagnostic.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Activer la journalisation pour Azure SQL Database ou les bases de données dans Managed Instance

Activez les métriques et la journalisation des diagnostics au niveau de SQL Database et des bases de données de Managed Instance ; elles ne sont pas activées par défaut.

Vous pouvez configurer les bases de données Azure SQL Database et les bases de données dans Managed Instance pour collecter les données de télémétrie de diagnostic suivantes :

| Analyse des données de télémétrie pour les bases de données | Prise en charge d’Azure SQL Database | Prise en charge des bases de données dans Managed Instance |
| :------------------- | ------------------- | ------------------- |
| [Toutes les métriques](sql-database-metrics-diag-logging.md#all-metrics) : Pourcentage DTU/CPU, Limite DTU/CPU, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP. | Oui | Non  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics) : contient des informations sur les statistiques d’exécution de requête comme les statistiques concernant l’utilisation du processeur et la durée des requêtes. | Oui | Oui |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics) : contient des informations sur les statistiques d’attente des requêtes (ce que vos requêtes ont attendu) comme CPU, LOG et LOCKING. | Oui | Oui |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset) : contient des informations sur les erreurs SQL au niveau de la base de données. | Oui | Non  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset) : contient des informations sur le temps que la base de données a passé à attendre différents types d’attente. | Oui | Non  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset) : contient des informations sur les délais d’expiration au niveau de la base de données. | Oui | Non  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset) : contient des informations sur les événements bloquants au niveau de la base de données. | Oui | Non  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset) : contient des informations Intelligent Insights relatives aux performances. Pour plus d’informations, consultez [Intelligent Insights](sql-database-intelligent-insights.md). | Oui | Oui |

### <a name="azure-portal"></a>Portail Azure

Le menu **Paramètres de diagnostic** vous permet de configurer le streaming des données de télémétrie de diagnostic pour chacune des bases de données Azure SQL Database et des bases de données de Managed Instance figurant sur le portail Azure. Vous pouvez définir les destinations suivantes : Stockage Azure, Azure Event Hubs et Azure Log Analytics.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour Azure SQL Database

   ![Icône de SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Pour activer le streaming des données de télémétrie de diagnostic pour Azure SQL Database, effectuez ces étapes :

1. Accédez à la ressource Azure SQL Database.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
   - Vous pouvez créer jusqu’à trois connexions parallèles pour le streaming des données de télémétrie de diagnostic.
   - Sélectionnez **+Ajouter un paramètre de diagnostic** pour configurer le streaming parallèle des données de diagnostic vers plusieurs ressources.

   ![Activer les diagnostics pour SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour une expérience de supervision standard basée sur les événements, cochez les cases suivantes pour les données de télémétrie de journal de diagnostic de base de données : **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** et **Deadlocks**.
1. Pour une expérience de supervision avancée à la minute, cochez la case **AllMetrics**.
1. Sélectionnez **Enregistrer**.

   ![Configurer les diagnostics pour SQL Database](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Les journaux d’audit de sécurité ne peuvent pas être activés à partir des paramètres de diagnostic de base de données. Pour activer le streaming des journaux d’audit, consultez [Configurer l’audit pour votre base de données](sql-database-auditing.md#subheading-2) et [SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).

> [!TIP]
> Répétez ces étapes pour chaque base de données Azure SQL Database que vous voulez superviser.

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour les bases de données dans Managed Instance

   ![Icône de base de données dans Managed Instance](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Pour activer le streaming des données de télémétrie de diagnostic pour des bases de données dans Managed Instance, effectuez ces étapes :

1. Accédez à la base de données dans Managed Instance.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.
   - Vous pouvez créer jusqu’à trois (3) connexions parallèles pour le streaming des données de télémétrie de diagnostic.
   - Sélectionnez **+Ajouter un paramètre de diagnostic** pour configurer le streaming parallèle des données de diagnostic vers plusieurs ressources.

   ![Activer les diagnostics pour une base de données dans Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Cochez les cases pour les données de télémétrie de diagnostic de base de données : **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** et **Errors**.
1. Sélectionnez **Enregistrer**.

   ![Configurer les diagnostics pour une base de données dans Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Répétez ces étapes pour chaque base de données de Managed Instance que vous voulez superviser.

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Activer la journalisation pour les pools élastiques ou Managed Instance

Activez la télémétrie de diagnostic pour les pools élastiques et Managed Instance faisant office de conteneurs de base de données. Leur propre fonction de télémétrie de diagnostic n’est pas activée par défaut.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour les pools élastiques

   ![Icône de pool élastique](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Vous pouvez configurer une ressource de pool élastique de sorte qu’elle collecte les données de télémétrie de diagnostic suivantes :

| Ressource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Pool élastique** | [Toutes les métriques](sql-database-metrics-diag-logging.md#all-metrics) : pourcentage eDTU/UC, limite eDTU/UC, pourcentage de lecture de données physiques, pourcentage d’écriture du journal, pourcentage de sessions, pourcentage de Workers, stockage, pourcentage de stockage, limite de stockage, pourcentage de stockage XTP. |

Pour activer le streaming des données de télémétrie de diagnostic pour une ressource de pool élastique, effectuez ces étapes :

1. Accédez à la ressource de pool élastique sur le portail Azure.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.

   ![Activer les diagnostics pour les pools élastiques](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour Log Analytics, sélectionnez **Configurer** et créez un espace de travail en sélectionnant **+Créer un espace de travail**, ou sélectionnez un espace de travail existant.
1. Cochez la case correspondant aux données de télémétrie de diagnostic de pool élastique **AllMetrics**.
1. Sélectionnez **Enregistrer**.

   ![Configurer les diagnostics pour les pools élastiques](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Répétez ces étapes pour chaque pool élastique que vous souhaitez superviser.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Configurer la diffusion en continu des données de télémétrie de diagnostic pour Managed Instance

   ![Icône Managed Instance](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Vous pouvez configurer une ressource Managed Instance de sorte qu’elle collecte les données de télémétrie de diagnostic suivantes :

| Resource | Supervision des données de télémétrie |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) contient le nombre de vCores, le pourcentage de processeur moyen, le requêtes d’E/S, les octets lus/écrits, l’espace de stockage réservé et l’espace de stockage utilisé. |

Pour activer le streaming des données de télémétrie de diagnostic pour une ressource Managed Instance, effectuez ces étapes :

1. Accédez à la ressource Managed Instance sur le portail Azure.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics** s’il n’existe aucun paramètre précédent, ou sélectionnez **Modifier le paramètre** pour modifier un paramètre précédent.

   ![Activer les diagnostics pour une instance Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Entrez un nom de paramètre pour référence personnelle.
1. Sélectionnez la ressource de destination pour les données de diagnostic de streaming : **Archiver dans un compte de stockage**, **Diffuser vers un hub d’événements** ou **Envoyer à Log Analytics**.
1. Pour Log Analytics, sélectionnez **Configurer** et créez un espace de travail en sélectionnant **+Créer un espace de travail**, ou utilisez un espace de travail existant.
1. Cochez la case correspondant aux données de télémétrie de diagnostic de pool élastique **ResourceUsageStats**.
1. Sélectionnez **Enregistrer**.

   ![Configurer les diagnostics pour une instance Managed Instance](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Répétez ces étapes pour chaque instance Managed Instance que vous souhaitez superviser.

### <a name="powershell"></a>PowerShell

Vous pouvez activer les métriques et la journalisation des diagnostics à l’aide de PowerShell.

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage de destination.

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

### <a name="to-configure-multiple-azure-resources"></a>Pour configurer plusieurs ressources Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell).

Indiquez l’ID de ressource d’espace de travail \<$WSID\> comme paramètre au moment d’exécuter le script `Enable-AzureRMDiagnostics.ps1` pour envoyer les données de diagnostic de plusieurs ressources vers l’espace de travail.

- Pour obtenir l’ID d’espace de travail \<$WSID\> de la destination de vos données de diagnostic, utilisez le script suivant :

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Remplacez \<subID\> par l’ID d’abonnement, \<RG_NAME\> par le nom de groupe de ressources, puis \<WS_NAME\> par le nom d’espace de travail.

### <a name="azure-cli"></a>Azure CLI

Vous pouvez activer les métriques et la journalisation des diagnostics à l’aide d’Azure CLI.

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L’ID de compte de stockage est l’ID de ressource du compte de stockage de destination.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L’ID de règle Service Bus est une chaîne au format suivant :

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="rest-api"></a>API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API RESTS Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Envoi vers Azure SQL Analytics

Azure SQL Analytics est une solution cloud qui supervise à grande échelle les performances des bases de données Azure SQL Database, des pools élastiques et de Managed Instance et entre plusieurs abonnements. Elle peut vous aider à collecter et visualiser des métriques de performances Azure SQL Database et intègre des fonctions d’intelligence destinées à résoudre les problèmes.

![Vue d’ensemble d’Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Les journaux de diagnostic et les métriques SQL Database peuvent être diffusés en continu vers Azure SQL Analytics en activant l’option intégrée **Envoyer à Log Analytics** sous l’onglet Paramètres de diagnostic du portail. Vous pouvez aussi activer Log Analytics à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface de ligne de commande Azure (CLI) ou l’API REST Azure Monitor.

### <a name="installation-overview"></a>Vue d’ensemble de l’installation

Vous pouvez superviser une flotte SQL Database avec Azure SQL Analytics. Effectuez les étapes suivantes :

1. Créez une solution Azure SQL Analytics à partir de la Place de marché Azure.
2. Créez un espace de travail de supervision dans la solution.
3. Configurez les bases de données pour qu’elles diffusent en continu les données de télémétrie de diagnostic dans l’espace de travail.

Si vous utilisez des pools élastiques ou Managed Instance, vous devez aussi configurer le streaming des données de télémétrie de diagnostic à partir de ces ressources.

### <a name="create-azure-sql-analytics-resource"></a>Créer une ressource Azure SQL Analytics

1. Recherchez et sélectionnez Azure SQL Analytics dans la Place de marché Azure.

   ![Rechercher Azure SQL Analytics dans le portail](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Sélectionnez **Créer** dans l’écran de présentation de la solution.

3. Complétez le formulaire Azure SQL Analytics avec les informations supplémentaires demandées : nom de l’espace de travail, abonnement, groupe de ressources, emplacement et niveau tarifaire.

   ![Configurer Azure SQL Analytics dans le portail](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Sélectionnez **OK** pour confirmer, puis **Créer**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurer des bases de données pour enregistrer des journaux de métriques et diagnostics

Le moyen le plus simple de configurer l’emplacement d’enregistrement des métriques par les bases de données est d’utiliser le portail Azure. Comme indiqué précédemment, accédez à vos ressources SQL Database sur le portail Azure, puis sélectionnez **Paramètres de diagnostic**.

Si vous utilisez des pools élastiques ou Managed Instance, vous devez aussi configurer les paramètres de diagnostic dans ces ressources pour permettre le streaming des données de télémétrie de diagnostic vers l’espace de travail.

### <a name="use-the-sql-analytics-solution"></a>Utiliser la solution SQL Analytics

Vous pouvez utiliser SQL Analytics comme un tableau de bord hiérarchique pour afficher vos ressources SQL Database. Pour découvrir comment utiliser la solution SQL Analytics, consultez [Surveiller une base de données SQL à l’aide de la solution SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmission en continu vers Event Hubs

Vous pouvez diffuser en continu les journaux de diagnostic et les métriques SQL Database dans Event Hubs à l’aide de l’option intégrée **Diffuser vers Event Hub** sur le portail. Vous pouvez également activer l’ID de règle Service Bus à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface CLI Azure ou l’API REST d’Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Que faire des journaux de métriques et diagnostics dans Event Hubs

Après avoir sélectionné les données envoyées à Event Hub, vous vous rapprochez de l’activation de scénarios d’analyse avancée. Event Hubs fait office de porte d’entrée pour un pipeline d’événements. Les données recueillies dans un hub d’événements peuvent ensuite être transformées et stockées à l’aide d’un fournisseur d’analytique en temps réel ou d’un adaptateur de stockage. Event Hubs dissocie la production d’un flux d’événements de la consommation de ces événements. De cette façon, les consommateurs d’événements peuvent accéder aux événements sur leur propre calendrier. Pour plus d’informations sur Event Hubs, consultez :

- [Qu’est-ce qu’Azure Event Hubs ?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Vous pouvez utiliser des métriques de streaming dans Event Hubs pour :

* **Afficher l’intégrité du service par diffusion en continu des données de chemin réactif vers Power BI**. En utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de métriques et de diagnostic en informations en temps réel sur vos services Azure. Pour une vue d’ensemble de la manière de configurer un concentrateur d’événements, de traiter les données avec Stream Analytics, et d’utiliser PowerBI comme sortie, voir [Stream Analytics et Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Diffuser en continu des journaux vers des flux de journalisation et de données de télémétrie tiers**. En utilisant le streaming Event Hubs, vous pouvez intégrer vos métriques et journaux de diagnostic à diverses solutions d’analytique et de supervision tierces.

* **Créer une plateforme de télémétrie et de journalisation personnalisée**. Vous disposez déjà d’une plateforme de télémétrie personnalisée ou envisagez d’en créer une ? La nature hautement scalable d’Event Hubs et de son modèle publication-abonnement vous permet d’ingérer les journaux de diagnostic avec souplesse. Lisez le [guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmission en continu dans le stockage

Vous pouvez stocker les métriques et les journaux de diagnostics SQL Database dans Stockage Azure en utilisant l’option intégrée **Archiver dans un compte de stockage** sur le portail. Vous pouvez aussi activer le stockage à l’aide d’un paramètre de diagnostic via les applets de commande PowerShell, l’interface de ligne de commande Azure (CLI) ou l’API REST Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma des journaux de métriques et diagnostics dans le compte de stockage

Après que vous avez configuré la collecte des journaux de métriques et diagnostics, lorsque les premières lignes de données sont disponibles, un conteneur de stockage est créé dans le compte de stockage que vous avez sélectionné. La structure des objets blob est la suivante :

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

Le nom d’un objet blob destiné à stocker les données d’un pool élastique se présente comme suit :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Télécharger les métriques et journaux du stockage

Découvrez comment [télécharger les journaux de métriques et de diagnostics à partir du stockage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Stratégie de rétention des données et tarification

Si vous sélectionnez Event Hubs ou un compte de stockage, vous pouvez spécifier une stratégie de rétention. Cette stratégie supprime les données antérieures à un intervalle de temps sélectionné. Si vous spécifiez Log Analytics, la stratégie de rétention dépend du niveau tarifaire sélectionné. Dans ce cas, les unités gratuites fournies pour l’ingestion de données permettent de superviser gratuitement plusieurs bases de données chaque mois. Toute utilisation de données de télémétrie de diagnostic au-delà des unités gratuites peut occasionner des frais. Sachez que les bases de données actives associées à de lourdes charges de travail ingèrent davantage de données que les bases de données inactives. Pour plus d’informations, consultez [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Si vous utilisez Azure SQL Analytics, vous pouvez superviser votre ingestion de données dans la solution en sélectionnant **Espace de travail OMS** dans le menu de navigation d’Azure SQL Analytics, puis **Utilisation** et **Coûts estimés**.

## <a name="metrics-and-logs-available"></a>Métriques et journaux disponibles

Vous pouvez utiliser les données de télémétrie de supervision collectées pour vos propres besoins _d’analyse personnalisée_ et de _développement d’applications_ avec le [langage SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). 

## <a name="all-metrics"></a>Toutes les métriques

Pour plus d’informations sur toutes les métriques par ressource, consultez les tableaux suivants.

### <a name="all-metrics-for-elastic-pools"></a>Toutes les métriques des pools élastiques

|**Ressource**|**Métriques**|
|---|---|
|Pool élastique|Pourcentage DTU, eDTU utilisé, Limite eDTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Pourcentage de sessions, Pourcentage de Workers, Stockage, Pourcentage de stockage, Limite de stockage, Pourcentage de stockage XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Toutes les métriques pour bases de données Azure SQL Database

|**Ressource**|**Métriques**|
|---|---|
|Base de données SQL Azure|Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP et blocages |

## <a name="logs-for-managed-instance"></a>Journaux Managed Instance

Consultez le tableau suivant pour obtenir des détails sur les journaux pour Managed Instance.

### <a name="resource-usage-statistics"></a>Statistiques d’utilisation des ressources

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|Type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : ResourceUsageStats |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : MANAGEDINSTANCES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom de l’instance Managed Instance |
|ResourceId|URI de ressource |
|SKU_s|Référence SKU produit Managed Instance |
|virtual_core_count_s|Nombre de vCores disponibles |
|avg_cpu_percent_s|Pourcentage d’UC moyenne |
|reserved_storage_mb_s|Capacité de stockage réservée sur Managed Instance |
|storage_space_used_mb_s|Stockage utilisé sur Managed Instance |
|io_requests_s|Nombre d’IOPS |
|io_bytes_read_s|Octets d’IOPS lus |
|io_bytes_written_s|Octets d’IOPS écrits |

## <a name="logs-for-azure-sql-databases-and-managed-instance-databases"></a>Journaux pour bases de données Azure SQL Database et Managed Instance

Pour obtenir des détails sur les journaux pour bases de données Azure SQL Database et Managed Instance, consultez les tableaux suivants.

### <a name="query-store-runtime-statistics"></a>Statistiques d’exécution du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics |
|OperationName|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|query_hash_s|Hachage de requête |
|query_plan_hash_s|Hachage de plan de requête |
|statement_sql_handle_s|Descripteur sql d’instruction |
|interval_start_time_d|Démarre le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|interval_end_time_d|Termine le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|logical_io_writes_d|Nombre total d’écritures E/S logiques |
|max_logical_io_writes_d|Nombre maximal d’écritures E/S logiques par exécution |
|physical_io_reads_d|Nombre total de lectures E/S physiques |
|max_physical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution |
|logical_io_reads_d|Nombre total de lectures E/S logiques |
|max_logical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution |
|execution_type_d|Type d’exécution |
|count_executions_d|Nombre d’exécutions de la requête |
|cpu_time_d|Temps processeur total consommé par la requête, en microsecondes |
|max_cpu_time_d|Consommateur de temps processeur maximal par une exécution unique, en microsecondes |
|dop_d|Somme des degrés de parallélisme |
|max_dop_d|Degré maximal de parallélisme utilisé pour une seule exécution |
|rowcount_d|Nombre total de lignes retournées |
|max_rowcount_d|Nombre maximal de lignes retournées dans une seule exécution |
|query_max_used_memory_d|Quantité totale de mémoire utilisée en Ko |
|max_query_max_used_memory_d|Quantité maximale de mémoire utilisée par une seule exécution, en Ko |
|duration_d|Durée d’exécution totale en microsecondes |
|max_duration_d|Durée d’exécution maximale d’une seule exécution |
|num_physical_io_reads_d|Nombre total de lectures physiques |
|max_num_physical_io_reads_d|Nombre maximal de lectures physiques par exécution |
|log_bytes_used_d|Quantité totale d’octets de journal utilisés |
|max_log_bytes_used_d|Quantité maximale d’octets de journal utilisés par exécution |
|query_id_d|ID de la requête dans le magasin des requêtes |
|plan_id_d|ID du plan dans le magasin des requêtes |

En savoir plus sur les [données de statistiques d’exécution du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiques d’attente du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : QueryStoreWaitStatistics |
|OperationName|Nom de l’opération. Toujours : QueryStoreWaitStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|wait_category_s|Catégorie de l’attente |
|is_parameterizable_s|Indique si la requête est paramétrable |
|statement_type_s|Type de l’instruction |
|statement_key_hash_s|Hachage de clé d’instruction |
|exec_type_d|Type d’exécution |
|total_query_wait_time_ms_d|Temps d’attente total de la requête dans la catégorie d’attente spécifique |
|max_query_wait_time_ms_d|Temps d’attente maximal de la requête dans une exécution individuelle sur la catégorie d’attente spécifique |
|query_param_type_d|0 |
|query_hash_s|Hachage de requête dans le magasin des requêtes |
|query_plan_hash_s|Hachage de plan de requêtes dans le magasin des requêtes |
|statement_sql_handle_s|Descripteur d’instruction dans le magasin des requêtes |
|interval_start_time_d|Démarre le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|interval_end_time_d|Termine le datetimeoffset de l’intervalle en nombre de cycles à partir du 01-01-1900 |
|count_executions_d|Comptabilisation des exécutions de la requête |
|query_id_d|ID de la requête dans le magasin des requêtes |
|plan_id_d|ID du plan dans le magasin des requêtes |

Découvrez-en davantage sur les [données des statistiques d’attente du magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Jeu de données d’erreurs

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQ |
|Catégorie|Nom de la catégorie. Toujours : Errors |
|OperationName|Nom de l’opération. Toujours : ErrorEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|Message|Message d’erreur en texte brut |
|user_defined_b|Indique si l’erreur est un bit défini par l’utilisateur |
|error_number_d|Code d'erreur |
|Severity|Gravité de l’erreur |
|state_d|État de l’erreur |
|query_hash_s|Hachage de requête de la requête ayant échoué, si disponible |
|query_plan_hash_s|Hachage du plan de requête de la requête ayant échoué, si disponible |

En savoir plus sur les [messages d’erreur SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Jeu de données de statistiques d’attente de base de données

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : DatabaseWaitStatistics |
|OperationName|Nom de l’opération. Toujours : DatabaseWaitStatisticsEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|wait_type_s|Nom du type d’attente |
|start_utc_date_t [UTC]|Heure de début de la période mesurée |
|end_utc_date_t [UTC]|Heure de fin de la période mesurée |
|delta_max_wait_time_ms_d|Temps d’attente maximal par exécution |
|delta_signal_wait_time_ms_d|Durée d’attente totale des signaux |
|delta_wait_time_ms_d|Durée d’attente totale dans la période |
|delta_waiting_tasks_count_d|Nombre de tâches en attente |

Apprenez-en davantage sur les [statistiques d’attente de base de données](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Jeu de données d’expirations

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Délais d’expiration |
|OperationName|Nom de l’opération. Toujours : TimeoutEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|error_state_d|Code d’état d’erreur |
|query_hash_s|Hachage de requête, si disponible |
|query_plan_hash_s|Hachage du plan de requête, si disponible |

### <a name="blockings-dataset"></a>Jeu de données de blocages

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Blocs |
|OperationName|Nom de l’opération. Toujours : BlockEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|lock_mode_s|Mode de verrouillage utilisé par la requête |
|resource_owner_type_s|Propriétaire du verrou |
|blocked_process_filtered_s|XML de rapport de processus bloqué |
|duration_d|Durée du verrou en microsecondes |

### <a name="deadlocks-dataset"></a>Jeu de données Deadlocks

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC] |Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : Blocages |
|OperationName|Nom de l’opération. Toujours : DeadlockEvent |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|deadlock_xml_s|XML de rapport de blocage |

### <a name="automatic-tuning-dataset"></a>Jeu de données AutomaticTuning

|Propriété|Description|
|---|---|
|TenantId|Votre ID d’abonné |
|SourceSystem|Toujours : Azure |
|TimeGenerated [UTC]|Horodatage indiquant à quel moment le journal a été enregistré |
|type|Toujours : AzureDiagnostics |
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL |
|Catégorie|Nom de la catégorie. Toujours : AutomaticTuning |
|Ressource|Nom de la ressource |
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES |
|SubscriptionId|GUID d’abonnement de la base de données |
|ResourceGroup|Nom du groupe de ressources de la base de données |
|LogicalServerName_s|Nom du serveur de la base de données |
|LogicalDatabaseName_s|Nom de la base de données |
|ElasticPoolName_s|Nom du pool élastique de la base de données, le cas échéant |
|DatabaseName_s|Nom de la base de données |
|ResourceId|URI de ressource |
|RecommendationHash_s|Hachage unique de la recommandation de réglage automatique |
|OptionName_s|Opération de paramétrage automatique |
|Schema_s|Schéma de base de données |
|Table_s|Table concernée |
|IndexName_s|Nom d’index |
|IndexColumns_s|Nom de la colonne |
|IncludedColumns_s|Colonnes incluses |
|EstimatedImpact_s|Impact estimé du JSON de recommandation de paramétrage automatique |
|Event_s|Type d’événement de paramétrage automatique |
|Timestamp_t|Horodatage de la dernière mise à jour |

### <a name="intelligent-insights-dataset"></a>Jeu de données Intelligent Insights

Apprenez-en davantage sur le [format de journal Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer la journalisation et comprendre les catégories de journaux et de métriques prises en charge par les différents services Azure, consultez :

* [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Vue d’ensemble des journaux de diagnostics Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Pour plus d’informations sur les concentrateurs d’événements, lisez :

* [Nouveautés des concentrateurs d’événements Azure ?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Pour plus d’informations sur Stockage Azure, consultez [Télécharger des métriques et des journaux de diagnostic à partir de Stockage Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
