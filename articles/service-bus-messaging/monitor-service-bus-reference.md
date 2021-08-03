---
title: Informations de référence sur l’analyse des données Azure Service Bus
description: Documentation de référence importante nécessaire pour analyser Azure Service Bus.
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: a32972b6400831a4045082fa5d0255b06ed66210
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061287"
---
# <a name="monitoring-azure-service-bus-data-reference"></a>Informations de référence sur l’analyse des données Azure Service Bus
Consultez [Supervision d’Azure Service Bus](monitor-service-bus.md) pour plus d’informations sur la collecte et l’analyse des données de surveillance d’Azure Service Bus.

## <a name="metrics"></a>Mesures
Cette section répertorie toutes les métriques de plateforme collectées automatiquement pour Azure Service Bus. Le fournisseur de ressources pour ces métriques est **Microsoft.ServiceBus/namespaces**.

### <a name="request-metrics"></a>Métriques de demande
Compte le nombre de requêtes d’opérations de données et de gestion.

| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| Demandes entrantes| Oui | Count | Total | Le nombre de requêtes effectuées auprès de Service Bus sur une période spécifiée. | EntityName | 
|Requêtes ayant réussi| Non | Count | Total | Le nombre de requêtes réussies effectuées auprès de Service Bus sur une période spécifiée. | Nom de l’entité<br/>OperationResult|
|Erreurs de serveur| Non | Count | Total | Nombre de requêtes non traitées en raison d'une erreur dans Service Bus sur une période spécifiée. | Nom de l’entité<br/>OperationResult|
|Erreurs d’utilisateur | Non | Count | Total | Nombre de requêtes non traitées en raison d'erreurs utilisateur sur une période spécifiée. | Nom de l’entité|
|Requêtes limitées| Non | Count | Total | Le nombre de demandes qui ont été limitées car l’utilisation a été dépassée. |  Nom de l’entité|

Les deux types d’erreurs suivants sont classées dans la catégorie des **erreurs d’utilisateur** :

1. Erreurs côté client (erreurs 400 dans HTTP).
2. Erreurs qui se produisent pendant le traitement des messages, par exemple [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


### <a name="message-metrics"></a>Métriques de message

| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Messages entrants| Oui | Count | Total | Le nombre d’événements ou de messages envoyés à Service Bus sur une période spécifiée. Cette métrique n’inclut pas les messages qui sont transférés automatiquement. | Nom de l’entité|
|Messages sortants| Oui | Count | Total | Le nombre d’événements ou de messages reçus à partir de Service Bus sur une période spécifiée. | Nom de l’entité|
| Messages| Non | Count | Average | Nombre de messages dans une file d’attente/rubrique. | Nom de l’entité |
| Messages actifs| Non | Count | Average | Nombre de messages actifs dans une file d’attente/rubrique. | Nom de l’entité |
| Messages de lettres mortes| Non | Count | Average | Nombre de messages de lettres mortes dans une file d’attente/rubrique.  | Nom de l’entité |
| Messages planifiés| Non | Count | Average | Nombre de messages planifiés dans une file d’attente/rubrique. | Nom de l’entité |
| Taille | Non | Octets | Average | Taille d’une entité (file d’attente ou rubrique) en octets. | Nom de l’entité | 

> [!NOTE]
> Valeurs des messages ; les messages actifs, de lettres mortes, planifiés, terminés et abandonnés possèdent des valeurs ponctuelles. Les messages entrants consommés immédiatement après ce moment peuvent ne pas apparaître dans ces métriques. 

### <a name="connection-metrics"></a>Métriques de connexion

| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Connexions actives| Non | Count | Total | Nombre de connexions actives sur un espace de noms, ainsi que sur une entité de l'espace de noms. La valeur de cette métrique est un instant dans le passé. Les connexions actives immédiatement après ce moment peuvent ne pas apparaître dans la métrique. | |
|Connexions ouvertes | Non | Count | Average | Le nombre de connexions ouvertes. | Nom de l’entité|
|Connexions fermées | Non | Count | Average | Le nombre de connexions fermées. | Nom de l’entité|

### <a name="resource-usage-metrics"></a>Métriques d’utilisation des ressources

> [!NOTE] 
> Les métriques suivantes sont disponibles uniquement avec le niveau **Premium**. 
> 
> Les principales métriques à surveiller en cas d'interruption pour un espace de noms du niveau Premium sont les suivantes : **Utilisation du processeur par espace de noms** et **Taille de la mémoire par espace de noms**. [Configurer des alertes](../azure-monitor/alerts/alerts-metric.md) pour ces métriques à l'aide d'Azure Monitor.
> 
> Vous pouvez également surveiller la métrique suivante : **Requêtes limitées**. Mais cela ne doit pas poser de problème tant que l'espace de noms reste dans les limites de sa mémoire, de son processeur et des connexions réparties. Pour plus d'informations, consultez [Limitation au niveau Premium d'Azure Service Bus](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier).

| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Utilisation du processeur par espace de noms| Non | UC | Pourcentage | Le pourcentage d’utilisation du processeur de l’espace de noms. | Réplica |
|Utilisation de la taille mémoire par espace de noms| Non | Utilisation de la mémoire | Pourcentage | Le pourcentage d’utilisation de mémoire de l’espace de noms. | Réplica |

### <a name="error-metrics"></a>Métriques d’erreur
| Nom de métrique |  Exportable par le biais des paramètres de diagnostic | Unité | Type d’agrégation |  Description | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Erreurs de serveur| Non | Count | Total | Nombre de requêtes non traitées en raison d'une erreur dans Service Bus sur une période spécifiée. | Nom de l’entité<br/><br/>Résultat de l’opération |
|Erreurs d’utilisateur | Non | Count | Total | Nombre de requêtes non traitées en raison d'erreurs utilisateur sur une période spécifiée. | Nom de l’entité<br/><br/>Résultat de l’opération|

## <a name="metric-dimensions"></a>Dimensions de métrique

Azure Service Bus prend en charge les dimensions suivantes pour les mesures dans Azure Monitor. L’ajout de dimensions à vos métriques est facultatif. Si vous n'ajoutez pas de dimensions, les métriques sont spécifiées au niveau de l'espace de noms. 

|Nom de la dimension|Description|
| ------------------- | ----------------- |
|Nom de l’entité| Service Bus prend en charge les entités de messagerie sous l’espace de noms.|

## <a name="resource-logs"></a>Journaux d’activité de ressources
Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour Azure Service Bus.

- Journaux des opérations
- Journaux de filtrage IP et de réseau virtuel

### <a name="operational-logs"></a>Journaux des opérations
Les entrées du journal des opérations incluent les éléments répertoriés dans le tableau suivant :

| Nom | Description |
| ------- | ------- |
| ActivityId | ID interne, utilisé pour identifier l’activité spécifiée |
| EventName | Nom d’opération |
| ResourceId | ID de ressource Azure Resource Manager |
| SubscriptionId | Identifiant d’abonnement |
| EventTimeString | Durée de l’opération |
| EventProperties | Propriétés de l’opération |
| Statut | État de l’opération |
| Appelant | Appelant de l’opération (le Portail Azure ou le client de gestion) |
| Category | OperationalLogs |

Voici un exemple de chaîne JSON du journal des opérations :

```json
{
  "ActivityId": "0000000000-0000-0000-0000-00000000000000",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/<AZURE SUBSCRPTION ID>/RESOURCEGROUPS/<RESOURCE GROUP NAME>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<SERVICE BUS NAMESPACE NAME>",
  "SubscriptionId": "0000000000-0000-0000-0000-00000000000000",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-00000000000000\",\"Namespace\":\"mynamespace\",\"Via\":\"https://mynamespace.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

### <a name="events-and-operations-captured-in-operational-logs"></a>Événements et opérations capturés dans les journaux des opérations
Les journaux des opérations capturent toutes les opérations de gestion qui sont effectuées sur l’espace de noms Azure Service Bus. Les opérations de données ne sont pas capturées en raison du volume élevé d’opérations de données effectuées sur Azure Service Bus.

> [!NOTE]
> Pour vous aider à améliorer le suivi des opérations de données, nous vous recommandons d’utiliser un suivi côté client.

Les opérations de gestion suivantes sont capturées dans les journaux des opérations : 

| Étendue | Opération|
|-------| -------- |
| Espace de noms | <ul> <li> Créer l'espace de noms</li> <li> Mettre à jour un espace de noms </li> <li> Supprimer un espace de noms </li> <li> Mettre à jour la stratégie SharedAccess d’espace de noms </li> </ul> | 
| File d'attente | <ul> <li> Créer une file d’attente</li> <li> Mettre à jour une file d’attente</li> <li> Supprimer une file d’attente </li> <li> Supprimer automatiquement une file d’attente </li> </ul> | 
| Rubrique | <ul> <li> Créer une rubrique </li> <li> Mettre à jour une rubrique </li> <li> Supprimer une rubrique </li> <li> Supprimer automatiquement une rubrique </li> </ul> |
| Abonnement | <ul> <li> Créer un abonnement </li> <li> Mettre à jour un abonnement </li> <li> Supprimer un abonnement </li> <li> Supprimer automatiquement un abonnement </li> </ul> |

> [!NOTE]
> Actuellement, les opérations de *lecture* ne font pas l’objet d’un suivi dans les journaux des opérations.

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs
Azure Service Bus utilise les tables Kusto des journaux d’activité Azure Monitor. Vous pouvez interroger ces tables à l’aide de Log Analytics. Pour obtenir la liste des tables Kusto utilisées par le service, consultez [Référence de table des Journaux Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#service-bus).


## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la supervision d’Azure Service Bus, consultez [Supervision d’Azure Service Bus](monitor-service-bus.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
