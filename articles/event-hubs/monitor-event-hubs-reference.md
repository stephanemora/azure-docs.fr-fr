---
title: Informations de référence sur la supervision des données Azure Event Hubs
description: Importantes ressources de référence nécessaires à la supervision d’Azure Event Hubs.
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 3d68f84c3c58cd29951c2d51cc8d89e3573b7f7a
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063655"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>Informations de référence sur la supervision des données Azure Event Hubs
Pour plus d’informations sur la collecte et l’analyse des données de supervision d’Azure Event Hubs, consultez [Supervision d’Azure Event Hubs](monitor-event-hubs.md).

## <a name="metrics"></a>Mesures
Cette section liste toutes les métriques de plateforme collectées automatiquement pour Azure Event Hubs. Le fournisseur de ressources pour ces métriques est **Microsoft.EventHub/clusters** ou **Microsoft.EventHub/clusters**.

### <a name="request-metrics"></a>Métriques de demande
Compte le nombre de requêtes d’opérations de données et de gestion.

| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| Demandes entrantes| Oui | Count | Total | Nombre de requêtes effectuées auprès du service Event Hubs sur une période spécifiée. | Nom de l’entité| 
| Requêtes ayant réussi| Non | Count | Total | Nombre de requêtes réussies auprès du service Event Hubs sur une période spécifiée. |  Nom de l’entité<br/><br/>Résultat de l’opération | 
| Requêtes limitées| Non | Count | Total |  Le nombre de demandes qui ont été limitées car l’utilisation a été dépassée. | Nom de l’entité<br/><br/>Résultat de l’opération |

Les deux types d’erreurs suivants sont classés dans la catégorie des **erreurs utilisateur** :

1. Erreurs côté client (erreurs 400 dans HTTP).
2. Erreurs qui se produisent lors du traitement des messages.


### <a name="message-metrics"></a>Métriques de message
| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Messages entrants|  Oui | Count | Total | Le nombre d’événements ou de messages envoyés à Event Hubs sur une période spécifiée. | Nom de l’entité|
|Messages sortants| Oui | Count | Total | Nombre d’événements ou de messages reçus d’Event Hubs sur une période spécifiée. | Nom de l’entité | 
| Messages capturés| Non | Count| Total | Nombre de messages capturés.  |  Nom de l’entité | 
|Octets entrants | Oui |  Octets | Total | Octets entrants pour un Event Hub sur une période spécifiée.  | Nom de l’entité| 
|Octets sortants | Oui |  Octets | Total |Octets sortants pour un Event Hub sur une période spécifiée.  | Nom de l’entité | 
| Taille | Non |  Octets | Average |  Taille d’un Event Hub en octets.|Nom de l’entité |


> [!NOTE]
> Ces valeurs sont des valeurs à un moment donné. Les messages entrants consommés immédiatement après ce moment peuvent ne pas apparaître dans ces métriques. 

### <a name="capture-metrics"></a>Métriques de capture
| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| Messages capturés| Non | Count| Total | Nombre de messages capturés.  | Nom de l’entité |
| Octets capturés | Non | Octets | Total | Octets capturés pour un Event Hub | Nom de l’entité | 
| Backlog des captures | Non | Count| Total | Backlog de capture pour un Event Hub | Nom de l’entité | 


### <a name="connection-metrics"></a>Métriques de connexion
| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Connexions actives| Non | Count | Average | Nombre de connexions actives sur un espace de noms, ainsi que sur une entité (Event Hub) de l’espace de noms. La valeur de cette métrique est un instant dans le passé. Les connexions actives immédiatement après ce moment peuvent ne pas apparaître dans la métrique.| Nom de l’entité | 
|Connexions ouvertes | Non | Count | Average |  Le nombre de connexions ouvertes. | Nom de l’entité | 
|Connexions fermées | Non | Count | Average|  Le nombre de connexions fermées. | Nom de l’entité | 

### <a name="error-metrics"></a>Métriques d’erreur
| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Erreurs de serveur| Non | Count | Total | Nombre de requêtes non traitées en raison d’une erreur dans le service Event Hubs sur une période spécifiée. | Nom de l’entité<br/><br/>Résultat de l’opération |
|Erreurs d’utilisateur | Non | Count | Total | Nombre de requêtes non traitées en raison d'erreurs utilisateur sur une période spécifiée. | Nom de l’entité<br/><br/>Résultat de l’opération|
|Erreurs de dépassement de quota | Non |Count | Total | Nombre d’erreurs provoquées par le dépassement des quotas sur une période spécifiée. | Nom de l’entité<br/><br/>Résultat de l’opération|


## <a name="metric-dimensions"></a>Dimensions de métrique

Azure Event Hubs prend en charge les dimensions suivantes pour les mesures dans Azure Monitor. L’ajout de dimensions à vos métriques est facultatif. Si vous n'ajoutez pas de dimensions, les métriques sont spécifiées au niveau de l'espace de noms. 

|Nom de la dimension|Description|
| ------------------- | ----------------- |
|Nom de l’entité| Nom du Event Hub.|

## <a name="resource-logs"></a>Journaux d’activité de ressources
[!INCLUDE [event-hubs-diagnostic-log-schema](../../includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs
Azure Event Hubs utilise les tables Kusto à partir des journaux Azure Monitor. Vous pouvez interroger ces tables avec Log Analytics. Pour obtenir la liste des tables Kusto utilisées par le service, consultez [Référence de table des Journaux Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs).


## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la supervision d’Azure Event Hubs, consultez [Supervision d’Azure Event Hubs](monitor-event-hubs.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
