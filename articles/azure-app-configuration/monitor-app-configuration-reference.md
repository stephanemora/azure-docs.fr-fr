---
title: Informations de référence sur les données de surveillance d’Azure App Configuration
description: Matériel de référence important nécessaire lorsque vous surveillez App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: ec6515403574af29c47b03b55eb3cbc42ecd4a7a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968344"
---
# <a name="monitoring-app-configuration-data-reference"></a>Informations de référence sur les données de surveillance d’App Configuration

Cet article est une référence concernant les données de surveillance collectées par App Configuration. Consultez [Supervision d’App Configuration](monitor-app-configuration.md) pour une présentation de la collecte et de l’analyse des données de surveillance pour App Configuration.

## <a name="metrics"></a>Mesures 
Type et fournisseur de ressources : [Métriques de la plateforme App Configuration](../azure-monitor/essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores)

| Métrique | Unité | Description |
|-------|-----| ----- |
| Nombre de requêtes HTTP entrantes   | Count | Nombre total de requêtes HTTP entrantes |
|Durée des requêtes HTTP entrantes | Millisecondes | Durée côté serveur d’une requête HTTP |
| Nombre de requêtes HTTP limitées | Count |    Les requêtes limitées sont des requêtes HTTP qui renvoient un code d’état 429 (trop de requêtes) |

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).


## <a name="metric-dimensions"></a>Dimensions de métrique
App Configuration a les dimensions suivantes associées à ses métriques.

| Nom de métrique | Description de la dimension |
|-------|-----|
| Nombre de requêtes HTTP entrantes | Nombre total de requêtes HTTP. Les dimensions prises en charge sont **HttpStatusCode** ou **AuthenticationScheme** de chaque requête. **AuthenticationScheme** peut être filtré par l’authentification AAD ou HMAC.   |
| Durée des requêtes HTTP entrantes | Durée côté serveur de chaque requête. Les dimensions prises en charge sont celles qui utilisent la dimension **HttpStatusCode** ou **AuthenticationScheme** de chaque requête. **AuthenticationScheme** peut être filtré par l’authentification AAD ou HMAC. |
| Nombre de requêtes HTTP limitées | Cette métrique n’a pas de dimensions. |

 Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Journaux d’activité de ressources
Cette section répertorie les types de catégories de journaux de ressources collectés pour App Configuration. 

| Type de journal de ressources | Informations supplémentaires|
|-------|-----|
| HttpRequest | [Informations relatives aux catégories de journaux de ressources pour App Configuration](../azure-monitor/essentials/resource-logs-categories.md) |

Pour plus d’informations, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).
 
## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Cette section fait référence à toutes les tables Kusto de Journaux d’activité Azure Monitor qui sont pertinentes pour App Configuration et qui peuvent être interrogées par Log Analytics.

|Type de ressource | Notes |
|-------|-----|
| [AACHttpRequest](/azure/azure-monitor/reference/tables/aachttprequest) | Entrées de chaque requête HTTP envoyée à une certaine ressource App Configuration. |
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | Entrées du journal d’activité Azure offrant un aperçu de tous les événements survenus dans Azure au niveau de l’abonnement ou du groupe d’administration. |

Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Tables de diagnostic

App Configuration utilise la [table AACHttpRequest](/azure/azure-monitor/reference/tables/aachttprequest) pour stocker les informations des journaux de ressources.

**Requêtes HTTP**

|Propriété | Type | Description |
|-------|-----| ----- |
|Category   |string |Catégorie de journal de l’événement, toujours HttpRequest. 
|ClientIPAddress |  string| Adresse IP du client qui a envoyé la requête.
|ClientRequestId|   string| ID de requête fourni par le client.
|CorrelationId| string| GUID des journaux corrélés.
|DurationMs|    int |Durée de l’opération en millisecondes.
|Method string| HTTP| Méthode de requête HTTP (GET ou POST).
|RequestId| string| ID de requête unique généré par le serveur.
|RequestLength| int |Longueur en octets de la requête HTTP.
|RequestURI|    string| URI de la requête, peut inclure la clé et le nom de l’étiquette. 
|_ResourceId|   string| Un identificateur unique de la ressource à laquelle l’enregistrement est associé
|ResponseLength|    int|    Longueur en octets de la réponse HTTP.
|SourceSystem| string|  
|StatusCode|    int |Code d’état HTTP de la requête.
|TenantId|  string  |WorkspaceId de la requête. 
|TimeGenerated| DATETIME|   Timestamp (UTC) où le journal a été généré parce qu’une requête a été envoyée.
|Type   |string|    Le nom de la table
|UserAgent| string| Agent utilisateur fourni par le client.


## <a name="see-also"></a>Voir aussi

* Pour obtenir une description de la supervision d’App Configuration, consultez [Supervision d’Azure App Configuration](monitor-app-configuration.md).
* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).