---
title: Vue d’ensemble des journaux de ressource Azure | Microsoft Docs
description: Découvrez les services pris en charge et le schéma d’événement pour les journaux de ressource Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551758"
---
# <a name="azure-resource-logs-overview"></a>Vue d’ensemble des journaux de ressource Azure
Les journaux de ressource Azure sont des [journaux de plateforme](platform-logs-overview.md) émis par des ressources Azure qui décrivent leur fonctionnement interne. Tous les journaux de ressource partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements.

> [!NOTE]
> Les journaux de ressource étaient auparavant appelés journaux de diagnostic.

## <a name="collecting-resource-logs"></a>Collecte des journaux de ressource
Les journaux de ressource sont générés automatiquement par les ressources Azure prises en charge, mais ils ne sont collectés que si vous les configurez à l’aide d’un [paramètre de diagnostic](diagnostic-settings.md). Créez un paramètre de diagnostic pour chaque ressource Azure pour transférer les journaux aux destinations suivantes :

| Destination | Scénario |
|:---|:---|:---|
| [Espace de travail Log Analytics](resource-logs-collect-storage.md) | Analysez les journaux avec d’autres données de surveillance et tirez parti des fonctionnalités d’Azure Monitor telles que les requêtes de journal et les alertes de journal. |
| [Azure Storage](archive-diagnostic-logs.md) | Archivez les journaux pour audit ou sauvegarde. |
| [Hub d’événements](resource-logs-stream-event-hubs.md) | Diffusez les journaux en continu sur des systèmes de journalisation et de télémétrie tiers.  |

## <a name="compute-resources"></a>Ressources de calcul
Les journaux de ressource sont différents des journaux au niveau du système d’exploitation invité dans les ressources de calcul Azure. Les ressources de calcul nécessitent qu’un agent collecte les journaux et les métriques à partir de leur système d’exploitation invité, notamment les données telles que les journaux d’événements, les journaux syslog et les compteurs de performances. Utilisez l’[Extension Diagnostic](agents-overview.md#azure-diagnostic-extension) pour router les données de journaux des machines virtuelles Azure et pour que l’[agent Log Analytics](agents-overview.md#log-analytics-agent) collecte les journaux et métriques des machines virtuelles dans Azure, dans d’autres clouds et localement dans un espace de travail Log Analytics. Pour plus de détails, consultez [Sources des données de surveillance pour Azure Monitor](data-sources.md).

## <a name="resource-logs-schema"></a>Schéma des journaux de ressource
Chaque service Azure possède son propre schéma lorsque les journaux de ressource sont écrits dans l’une des destinations, mais tous partagent le même schéma de niveau supérieur dans le tableau suivant. Consultez la rubrique [Schémas par service](#service-specific-schemas) ci-dessous pour voir le schéma correspondant à chaque service. 

| Nom | Obligatoire ou facultatif | Description |
|---|---|---|
| time | Obligatoire | Horodatage (heure UTC) de l’événement. |
| resourceId | Obligatoire | ID de la ressource qui a émis l’événement. Pour les services abonnés, cet ID prend la forme /tenants/tenant-id/providers/provider-name. |
| tenantId | Obligatoire pour les journaux d’activité de l’abonné | ID d’abonné de l’abonné Active Directory auquel cet événement est lié. Cette propriété est utilisée uniquement pour les journaux d’activité de niveau abonné ; elle n’apparaît pas dans les journaux d’activité de niveau ressource. |
| operationName | Obligatoire | Nom de l’opération représentée par cet événement. Si l’événement représente une opération RBAC, il s’agit du nom de l’opération RBAC (par ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Généralement modélisée sous la forme d’une opération du Gestionnaire de ressources, même s’il ne s’agit pas d’opérations réelles documentées du Gestionnaire des ressources (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facultatif | Version d’api associée à l’opération, si operationName a été effectuée à l’aide d’une API (par ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). S’il n’existe aucune API qui corresponde à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer. |
| category | Obligatoire | Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux d’activité sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob de propriétés d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique. Les catégories de journaux classiques sont « Audit » « opérationnel » « Exécution » et « Demande ». |
| resultType | Facultatif | État de l’événement. Les valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active et Resolved. |
| resultSignature | Facultatif | Sous-état de l’événement. Si cette opération correspond à un appel d’API REST, il s’agit du code d’état HTTP de l’appel REST correspondant. |
| resultDescription | Facultatif | Description textuelle statique de cette opération, par ex. « Obtenir le fichier de stockage ». |
| durationMS | Facultatif | Durée de l’opération en millisecondes. |
| callerIpAddress | Facultatif | Adresse IP de l’appelant, si l’opération correspond à un appel d’API qui proviendrait d’une entité avec une adresse IP disponible publiquement. |
| correlationId | Facultatif | GUID utilisé pour regrouper un ensemble d’événements associés. En règle générale, si deux événements ont le même operationName, mais deux états différents (par ex., « Started » et « Succeeded »), ils partagent le même ID de corrélation. Cela peut également représenter d’autres relations entre les événements. |
| identité | Facultatif | Objet blob JSON qui décrit l’identité de l’utilisateur ou d’une application qui a effectué l’opération. En général, cela inclut l’autorisation et les revendications/Jeton JWT issus d’active directory. |
| Niveau | Facultatif | Niveau de gravité de l’événement. Doit être Information, Avertissement, Erreur, ou Critique. |
| location | Facultatif | Région de la ressource générant l’événement, par ex. USA Est ou France Sud |
| properties | Facultatif | Toutes les propriétés étendues associées à cette catégorie particulière d’événements spécifiques. Toutes les propriétés personnalisées/uniques doivent être placées à l’intérieur de cette « Partie B » du schéma. |

## <a name="service-specific-schemas"></a>Schémas par service
Le schéma des journaux de ressource varie en fonction du type de ressource défini par la propriété `resourceId` et les propriétés `category`. La liste suivante répertorie tous les services Azure qui prennent en charge des journaux de ressource ainsi que les liens associés à chaque service et au schéma correspondant le cas échéant.

| de diffusion en continu | Schéma et documentation |
| --- | --- |
| Azure Active Directory | [Vue d’ensemble](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Schéma des journaux d’audit](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) et [Schéma des connexions](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestion des API | [Journaux de diagnostic Gestion des API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Passerelles d’application |[Journalisation des diagnostics pour Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation des diagnostics Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database pour MySQL | [Journaux de diagnostic Azure Database pour MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database pour PostgreSQL | [Journaux de diagnostic Azure Database pour PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Journalisation des diagnostics pour Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Réseau de distribution de contenu | [Journaux de diagnostic Azure pour CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Journalisation Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Surveiller les fabriques de données à l’aide d’Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accès aux journaux de diagnostic d’Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Journaux de diagnostic d’Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma non disponible. |
| Pare-feu Azure | Schéma non disponible. |
| IoT Hub | [Opérations IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Journalisation d’Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Analytique des journaux de l'équilibreur de charge Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Group |[Analytique des journaux pour les groupes de sécurité réseau (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protection DDOS | [Gérer le service Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dédiées | [Journalisation des diagnostics pour Power BI Embedded dans Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Modèle de données pour la sauvegarde Azure](../../backup/backup-azure-reports-data-model.md)|
| Recherche |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../../search/search-traffic-analytics.md) |
| Service Bus |[Journaux de diagnostic Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Base de données SQL | [Journalisation de diagnostic Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Journaux de diagnostic des travaux](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma des journaux de Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | Schéma non disponible. |
| Passerelles de réseau virtuel | Schéma non disponible. |

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez les autres journaux de plateforme Azure](platform-logs-overview.md) que vous pouvez utiliser pour surveiller Azure.
