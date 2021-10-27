---
title: Services et schémas pris en charge pour les journaux de ressources Azure
description: Découvrez les services pris en charge et les schémas d’événement pour les journaux de ressources Azure.
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 1ef9c5ecea1ff45c7e6b3501f7c71928a5ea60d6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069460"
---
# <a name="common-and-service-specific-schemas-for-azure-resource-logs"></a>Schémas communs et spécifiques de services pour les journaux de ressources Azure

> [!NOTE]
> Les journaux de ressource étaient auparavant appelés journaux de diagnostic. Le nom a été modifié en octobre 2019 parce que les types de journaux collectés par Azure Monitor ont évolué pour inclure plus que la seule ressource Azure. 
>
> Cet article répertoriait les catégories de journaux de ressources que vous pouvez collecter. Cette liste se trouve à présent dans les [Catégories de journaux de ressources](resource-logs-categories.md). 

Les [journaux de ressource Azure Monitor](../essentials/platform-logs-overview.md) sont des journaux d’activité générés par les services Azure, qui décrivent le fonctionnement de ces services ou ressources. Tous les journaux de ressources disponibles via Azure Monitor partagent un schéma de niveau supérieur commun. Chaque service a la possibilité d’émettre des propriétés uniques pour ses propres événements.

Une combinaison du type de ressource (disponible dans la propriété `resourceId`) et la catégorie identifient de manière unique un schéma. Cet article décrit les schémas de niveau supérieur pour les journaux de ressources et les liens vers les schémas pour chaque service.


## <a name="top-level-common-schema"></a>Schéma commun de niveau supérieur

> [!NOTE]
> Le schéma décrit ici est valide lorsque des journaux de ressources sont envoyés à Stockage Azure ou à un Event Hub. Lorsque les journaux sont envoyés à un espace de travail Log Analytics, les noms des colonnes peuvent être différents. Pour plus d’informations sur les colonnes communes à toutes les tables d’un espace de travail Log Analytics et sur la [référence de données Azure Monitor](/azure/azure-monitor/reference) pour une référence de tables différentes, consultez [Colonnes standard dans les journaux Azure Monitor](../logs/log-standard-columns.md).

| Nom | Obligatoire ou facultatif | Description |
|---|---|---|
| `time` | Obligatoire | Horodatage (heure UTC) de l’événement. |
| `resourceId` | Obligatoire | ID de la ressource qui a émis l’événement. Pour les services abonnés, cet ID prend la forme */tenants/tenant-id/providers/provider-name*. |
| `tenantId` | Obligatoire pour les journaux d’activité de l’abonné | ID d’abonné de l’abonné Active Directory auquel cet événement est lié. Cette propriété est utilisée uniquement pour les journaux au niveau du locataire. Elle n’apparaît pas dans les journaux au niveau de la ressource. |
| `operationName` | Obligatoire | Nom de l’opération représentée par cet événement. Si l’événement représente une opération de contrôle d’accès en fonction du rôle (RBAC) Azure, il s’agit du nom de l’opération Azure RBAC (par exemple, `Microsoft.Storage/storageAccounts/blobServices/blobs/Read`). Ce nom est généralement modélisé sous la forme d’une opération d’Azure Resource Manager, même s’il ne s’agit pas d’une opération réelle documentée de Resource Manager : (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| `operationVersion` | Facultatif | Version d’API associée à l’opération, si `operationName` a été effectué à l’aide d’une API (par exemple `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Si aucune API ne correspond à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer. |
| `category` | Obligatoire | Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux d’activité sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob de propriétés d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique. Les catégories de journal standard sont `Audit`, `Operational`, `Execution` et `Request`. |
| `resultType` | Facultatif | État de l’événement. Les valeurs standard sont `Started`, `In Progress`, `Succeeded`, `Failed`, `Active` et `Resolved`. |
| `resultSignature` | Facultatif | Sous-état de l’événement. Si cette opération correspond à un appel d’API REST, ce champ est le code d’état HTTP de l’appel REST correspondant. |
| `resultDescription `| Facultatif | Description textuelle statique de cette opération, par ex. `Get storage file`. |
| `durationMs` | Facultatif | Durée de l’opération en millisecondes. |
| `callerIpAddress` | Facultatif | Adresse IP de l’appelant, si l’opération correspond à un appel d’API qui provient d’une entité avec une adresse IP disponible publiquement. |
| `correlationId` | Facultatif | GUID utilisé pour regrouper un ensemble d’événements associés. En règle générale, si deux événements ont le même `operationName`, mais deux états différents (par exemple `Started` et `Succeeded`), ils partagent la même valeur `correlationID`. Cela peut également représenter d’autres relations entre les événements. |
| `identity` | Facultatif | Objet blob JSON qui décrit l’identité de l’utilisateur ou d’une application qui a effectué l’opération. En général, ce champ inclut l’autorisation et les revendications ou Jeton JWT issus d’Active Directory. |
| `Level` | Facultatif | Niveau de gravité de l’événement. Doit être `Informational`, `Warning`, `Error` ou `Critical`. |
| `location` | Facultatif | Région de la ressource générant l’événement, par ex. `East US` ou `France South`. |
| `properties` | Facultatif | Toutes les propriétés étendues associées à cette catégorie d’événements. Toutes les propriétés personnalisées ou uniques doivent être placées à l’intérieur de cette « Partie B » du schéma. |

## <a name="service-specific-schemas"></a>Schémas par service

Le schéma des journaux des ressources varie en fonction de la ressource et de la catégorie de journal d’activité. La liste suivante répertorie les services Azure qui mettent des journaux de ressources et des liens à disposition du service et des schémas spécifiques à la catégorie (lorsqu’ils sont disponibles). La liste change au fur et à mesure de l’ajout de services. Si vous ne voyez pas ce dont vous avez besoin, n’hésitez pas à ouvrir un problème GitHub dans cet article afin de pouvoir le mettre à jour.

| Service ou fonctionnalité | Schéma et documentation |
| --- | --- |
| Azure Active Directory | [Vue d’ensemble](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Schéma des journaux d’audit](../../active-directory/reports-monitoring/overview-reports.md), [Schéma des connexions](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Azure Analysis Services | [Azure Analysis Services : Configurer la journalisation des diagnostics](../../analysis-services/analysis-services-logging.md) |
| Gestion des API Azure | [Journaux de ressources de Gestion des API](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Azure App Service | [Journaux App Service](../../app-service/troubleshoot-diagnostic-logs.md)
| Azure Application Gateway |[Journalisation pour Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation d’Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Cognitive Services | [Journalisation pour Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Azure Container Instances | [Journalisation pour Azure Container Instances](../../container-instances/container-instances-log-analytics.md#log-schema) |
| Azure Container Registry | [Journalisation pour Azure Container Registry](../../container-registry/monitor-service.md) |
| Azure Content Delivery Network | [Journaux de diagnostic pour Azure Content Delivery Network](../../cdn/cdn-azure-diagnostic-logs.md) |
| Azure Cosmos DB | [Journalisation Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Explorateur de données Azure | [Journaux d’Azure Data Explorer](/azure/data-explorer/using-diagnostic-logs) |
| Azure Data Factory | [Surveiller Data Factory à l’aide d’Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Service Analytique Azure Data Lake |[Accès aux journaux d’Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Azure Data Lake Storage |[Accès aux journaux d’Azure Data Lake Storage](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Database pour MySQL | [Journaux de diagnostic Azure Database pour MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database pour PostgreSQL | [Journaux d’Azure Database pour PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Journalisation des diagnostics dans Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Protection DDoS dans Azure | [Journalisation pour Azure DDoS Protection Standard](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Configurer les diagnostics Azure Digital Twins](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Hubs d'événements Azure |[Journaux d’Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Azure ExpressRoute | Schéma non disponible |
| Pare-feu Azure | [Journalisation pour le Pare-feu Azure](../../firewall/logs-and-metrics.md#diagnostic-logs) |
| Azure Front Door | [Journalisation pour Azure Front Door](../../frontdoor/front-door-diagnostics.md) |
| Azure IoT Hub | [Opérations IoT Hub](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Azure Key Vault |[Journalisation d’Azure Key Vault](../../key-vault/general/logging.md) |
| Azure Kubernetes Service |[Journalisation Azure Kubernetes Service](../../aks/monitor-aks-reference.md#resource-logs) |
| Azure Load Balancer |[Analytique des journaux d'activité pour Azure Load Balancer](../../load-balancer/monitor-load-balancer.md) |
| Azure Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Machine Learning | [Journalisation de diagnostic dans Azure Machine Learning](../../machine-learning/monitor-resource-reference.md) |
| Azure Media Services | [Schémas de surveillance Media Services](../../media-services/latest/monitoring/monitor-media-services-data-reference.md#schemas) |
| Groupes de sécurité réseau |[Analytique des journaux d'activité pour les groupes de sécurité réseau (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Azure Power BI Embedded | [Journalisation pour Power BI Embedded dans Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Modèle de données pour Sauvegarde Azure](../../backup/backup-azure-reports-data-model.md)|
| Rechercher l’analyse du trafic |[Activation et utilisation de la recherche d’analyse du trafic](../../search/search-traffic-analytics.md) |
| Azure Service Bus |[Journaux d’Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Azure SQL Database | [Journalisation d’Azure SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stockage Azure | [Objets Blob](../../storage/blobs/monitor-blob-storage-reference.md#resource-logs-preview), [Fichiers](../../storage/files/storage-files-monitoring-reference.md#resource-logs-preview), [Files d’attente](../../storage/queues/monitor-queue-storage-reference.md#resource-logs-preview), [Tables](../../storage/tables/monitor-table-storage-reference.md#resource-logs-preview) |
| Azure Stream Analytics |[Journaux de tâches](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Azure Traffic Manager | [Schéma des journaux de Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Réseau virtuel Azure | Schéma non disponible |
| Passerelles de réseau virtuel | Schéma non disponible |



## <a name="next-steps"></a>Étapes suivantes

* [Voir les catégories de journaux de ressources que vous pouvez collecter](resource-logs-categories.md)
* [En savoir plus sur les journaux de ressources](../essentials/platform-logs-overview.md)
* [Diffuser en continu les journaux de ressource vers Event Hubs](./resource-logs.md#send-to-azure-event-hubs)
* [Modifier les paramètres de diagnostic de journal de ressources à l’aide de l’API REST Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analyser les journaux d’activité de Stockage Azure avec Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)
